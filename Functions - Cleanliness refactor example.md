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
