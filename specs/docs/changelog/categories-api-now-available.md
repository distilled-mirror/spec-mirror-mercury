Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Categories API now supports create, edit, and delete

We've expanded the Mercury API with three new `/api/v1/categories` endpoints that make it easier to manage expense categories for your organization.

[Create Category](https://docs.mercury.com/reference/createcategory)

You can now create a new expense category by providing a `name` along with three required visibility flags: `visibleForCardSpend` (whether the category applies to card transactions), `visibleForOther` (whether it applies to all other transaction kinds), and `visibleForReimbursements` (whether it applies to expense reimbursement transactions). The endpoint returns the newly created category, including its `expenseCategoryId` so you can immediately reference it elsewhere.

[Edit Category](https://docs.mercury.com/reference/editcategory)

You can update an existing category by providing its `expenseCategoryId` along with any combination of optional fields: `name` (a new name for the category), `visibleForCardSpend` (whether the category applies to card transactions), `visibleForOther` (whether it applies to all other transaction kinds), and `visibleForReimbursements` (whether it applies to expense reimbursement transactions). The endpoint returns the updated category, so you can immediately confirm the new state.

[Delete Category](https://docs.mercury.com/reference/deletecategory)

This endpoint lets you delete a category by its `expenseCategoryId`.

If you have questions or want to see additional category related functionality in future releases, feel free to reach out to <api@mercury.com>.
