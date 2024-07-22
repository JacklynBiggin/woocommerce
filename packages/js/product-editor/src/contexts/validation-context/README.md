# Validations and error handling

This directory contains some components and hooks used for validations in the product editor.

See [Product Editor Development Handbook](../../../../../../docs/product-editor-development/README.md)
for more information.

## Add a link to error snackbar

### What happens when there is an error in the form?

1. Every field [will be validated](https://github.com/woocommerce/woocommerce/blob/trunk/packages/js/product-editor/src/contexts/validation-context/validation-provider.tsx#L87-L110).

2. An object consisting of the error/validation message, the context, and the validatorId will be returned ([link](https://github.com/woocommerce/woocommerce/blob/trunk/packages/js/product-editor/src/contexts/validation-context/validation-provider.tsx#L74) ).
The `context` contains the block Id, and the `validatorId` a unique ID for the validator specifically ( generally a prefix with the block id ).

3. If, for instance, the name field is empty, the validation will fail and will throw an object like this:

    ```javascript
    { message: 'Product name is required.'; context: [block id]; validatorId: [prefix + block id] }
    ```

4. This is the result of the [name validator](https://github.com/woocommerce/woocommerce/blob/trunk/packages/js/product-editor/src/blocks/product-fields/name/edit.tsx#L84-L101) and the [validatorId addition](https://github.com/woocommerce/woocommerce/blob/trunk/packages/js/product-editor/src/contexts/validation-context/validation-provider.tsx#L69).

5. Then, [the props needed](https://github.com/woocommerce/woocommerce/blob/trunk/packages/js/product-editor/src/components/header/publish-button/publish-button.tsx#L65-L71) to show the error snackbar will be returned.

    - When the field with [the error is not visible](https://github.com/woocommerce/woocommerce/blob/trunk/packages/js/product-editor/src/hooks/use-error-handler.ts#L105), a link pointing to it will be added to the snackbar.
    - Otherwise, the error will be dismissed automatically.

6. The hook `useErrorHandler` is used to get the [error props](https://github.com/woocommerce/woocommerce/blob/trunk/packages/js/product-editor/src/hooks/use-error-handler.ts#L79).

    - The error shown will depend [on the error code](https://github.com/woocommerce/woocommerce/blob/trunk/packages/js/product-editor/src/hooks/use-error-handler.ts#L92).
    - [As you can see here](https://github.com/woocommerce/woocommerce/blob/trunk/packages/js/product-editor/src/hooks/use-error-handler.ts#L157-L162), if the error doesn't have a code, the default message will be `Failed to save product.`
    - The context is used to [get the parent tab](https://github.com/woocommerce/woocommerce/blob/trunk/packages/js/product-editor/src/hooks/use-blocks-helper/use-blocks-helper.ts#L7) id and the validatorId to [focus on the field](https://github.com/woocommerce/woocommerce/blob/trunk/packages/js/product-editor/src/hooks/use-error-handler.ts#L68).

Finally, [the snackbar with the error](https://github.com/woocommerce/woocommerce/blob/trunk/packages/js/product-editor/src/components/header/publish-button/publish-button.tsx#L70) message and props will be displayed.

### Limitations

The server errors, such as `duplicated SKU`, are not being mapped yet.
