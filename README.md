##Blue Sky API

I’d like to see an interface where developers can easily alter components via dot operators like we know from jQuery.

When the build runs you can target all the code blocks in a specific file as an example lets use the following code
```
import parse from 'html-react-parser';
import React, { Fragment } from 'react';
import { func, shape, string } from 'prop-types';
import {
    parseAnchor,
    parseNode,
    parseImage,
    parseCustomForm,
    parseHeading,
} from './parsers';

const defaultParsers = {
    a: parseAnchor,
    b: parseNode,
    strong: parseNode,
    img: parseImage,
    h1: parseHeading,
    h2: parseHeading,
    h3: parseHeading,
    h4: parseHeading,
};

const defaultWidgetParsers = {
    Amasty_Customform: parseCustomForm,
};

const transform = parsers => node => {
    const text = node.data;

    // Parse custom widgets
    if (text?.includes('{{widget')) {
        const templateType = text
            .split('template="')
            .pop()
            .split('::')[0];
        const parser = parsers[templateType];

        if (!parser) {
            return node;
        }

        return parser(node);
    }

    const parser = parsers[node.name];

    if (!parser) {
        return node;
    }
    return parser(node);
};

/**
 * The wysiwyg component will parse html strings to react components using our parsers
 */
export const Wysiwyg = ({ children, parsers }) => {
    return (
        <Fragment>
            {parse(children, {
                replace: transform({
                    ...defaultParsers,
                    ...defaultWidgetParsers,
                    ...parsers,
                }),
            })}
        </Fragment>
    );
};
```
In the blue sky api this wil be returned as an object with methods
```
const api = {
    imports: [
        'html-react-parser',
        'react',
        'prop-types',
        './parsers'
    ],
    variables: [
        defaultParsers,
        defaultWidgetParsers,
        transform
    ],
}
```
So if i'd like to change the value of `defaultWidgetParsers` I can do the following:

```
variables.defaultWidgetParsers.replace({}) // would set variable to empty object  
variables.defaultWidgetParsers.append({Aheadworks_productList: parseAheadworksProductList}) // would append the value to the object
variables.defaultWidgetParsers.prepend({Aheadworks_productList: parseAheadworksProductList}) // would prepend the value to the object
variables.defaultWidgetParsers.deleteByKey(Aheadworks_productList)
```
