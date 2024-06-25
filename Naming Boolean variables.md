#good-practice #clean-code  

Boolean variables will mostly be used as flags, and thus they **must** start with an `is` prefix, or a [modal verb]([Modal Verbs: Definition & Usage Examples | Grammarly Blog](https://www.grammarly.com/blog/modal-verbs/)). [Action verbs]([English Grammar Rules - Do, Does, Did (gingersoftware.com)](https://www.gingersoftware.com/content/grammar-rules/verbs/do-does-did))  can also be used, although should be avoided, preferring `is` or modal verbs.

Some developers like to use some **verbs** for naming boolean variables, but this is **strongly discouraged**. Because [[Method names should be verbs|verbs should be reserved for method names]] only. 

If you read `enableCheckoutButton` what do you think it is? A boolean? Or a method?

Instead if you rename it to `isCheckoutButtonEnabled`, now it is clear that the variable is a boolean. 