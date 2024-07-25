#clean-code
# Code #1

Read the following code and try to *decipher* what is going on for 3-5minutes:

```typescript
const testableHtml = async (pageData: PageData, includeSuiteSetup: boolean): Promise<string> => {
  const wikiPage: WikiPage = pageData.getWikiPage();
  let buffer: string = '';

  if (pageData.hasAttribute('Test')) {
    if (includeSuiteSetup) {
      const suiteSetup: WikiPage | null = await PageCrawlerImpl.getInheritedPage(SuiteResponder.SUITE_SETUP_NAME, wikiPage);
      if (suiteSetup !== null) {
        const pagePath: WikiPagePath = suiteSetup.getPageCrawler().getFullPath(suiteSetup);
        const pagePathName: string = PathParser.render(pagePath);
        buffer += `!include -setup .${pagePathName}\n`;
      }
    }

    const setup: WikiPage | null = await PageCrawlerImpl.getInheritedPage('SetUp', wikiPage);
    if (setup !== null) {
      const setupPath: WikiPagePath = wikiPage.getPageCrawler().getFullPath(setup);
      const setupPathName: string = PathParser.render(setupPath);
      buffer += `!include -setup .${setupPathName}\n`;
    }
  }

  buffer += pageData.getContent();

  if (pageData.hasAttribute('Test')) {
    const teardown: WikiPage | null = await PageCrawlerImpl.getInheritedPage('TearDown', wikiPage);
    if (teardown !== null) {
      const tearDownPath: WikiPagePath = wikiPage.getPageCrawler().getFullPath(teardown);
      const tearDownPathName: string = PathParser.render(tearDownPath);
      buffer += `\n!include -teardown .${tearDownPathName}\n`;
    }

    if (includeSuiteSetup) {
      const suiteTeardown: WikiPage | null = await PageCrawlerImpl.getInheritedPage(SuiteResponder.SUITE_TEARDOWN_NAME, wikiPage);
      if (suiteTeardown !== null) {
        const pagePath: WikiPagePath = suiteTeardown.getPageCrawler().getFullPath(suiteTeardown);
        const pagePathName: string = PathParser.render(pagePath);
        buffer += `!include -teardown .${pagePathName}\n`;
      }
    }
  }

  pageData.setContent(buffer);
  return pageData.getHtml();
}
```

Damn! This is a huge mess, and is not even that complicated! But reading through this is difficult, is strange functions being called, there are double nested `if` statements, there is duplicated code.

# Code #2

Let's take a look at this refactor:

```typescript
const renderPageWithSetupsAndTeardowns = async (pageData: PageData, isSuite: boolean): Promise<string> => {
  const isTestPage: boolean = pageData.hasAttribute('Test');
  if (isTestPage) {
    const testPage: WikiPage = pageData.getWikiPage();
    let newPageContent: string = '';

    await this.includeSetupPages(testPage, newPageContent, isSuite);
    newPageContent += pageData.getContent();
    await this.includeTeardownPages(testPage, newPageContent, isSuite);

    pageData.setContent(newPageContent);
  }

  return pageData.getHtml();
}
```

It is possible you don't fully understand what is going without all the details but still, you probably understand that this function performs the inclusion of *some* `setup` and `teardown` pages into a test page and then renders that page into HTML. 

Divining this information is easier in the refactor but pretty much impossible on the first example.

# Code #3

Remember the main rule for functions?

![[Functions - First Rule]]

So how do we make this **even smaller?**

```typescript
const renderPageWithSetupsAndTeardowns = async (pageData: PageData, isSuite: boolean): Promise<string> =>  {
  if (this.isTestPage(pageData)) {
    await this.includeSetupAndTeardownPages(pageData, isSuite);
  }
  return pageData.getHtml();
}
```

## Is it doing one thing only?

We established that [[Functions should do one thing]] and one thing only.

But isn't this function (even refactored) doing multiple things?
* Determining whether or not is a test page
* If it is, include the setup and teardown pages
* Render the html page

Although this function may seem it does multiple things, in reality is just under one level of abstraction, which can be described as 

> "To renderPageWithSetupsAndTeardowns we check to see whether the page is a test page and if so, we include the setups and teardowns. In either case we render the page in HTML."

We *could* extract the `if` statement into a separate function named `includeSetupsAndTeardownsIfTestPage` but that simply restates the code without changing the level of abstraction. 

A way we can determine or not a function is doing multiple things is you can extract another function from it with a name that is not merely a restatement of its implementation.