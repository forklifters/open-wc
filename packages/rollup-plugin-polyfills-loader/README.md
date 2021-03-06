# Rollup Plugin Polyfills Loader

Inject [Polyfills Loader](https://github.com/open-wc/open-wc/tree/master/packages/polyfills-loader) into a HTML file generated by [@open-wc/rollup-plugin-html](https://github.com/open-wc/open-wc/tree/master/packages/rollup-plugin-html).

## Under construction

Documentation is still under construction.

## Types

```ts
import { PolyfillsConfig } from 'polyfills-loader';

export interface LegacyBuildConfig {
  name: string;
  test: string;
}

export interface PluginOptions {
  htmlFileName?: string;
  modernOutput?: string;
  legacyOutput?: LegacyBuildConfig | LegacyBuildConfig[];
  polyfills?: PolyfillsConfig;
}
```

## Examples

### Basic rollup build

```js
import html from '@open-wc/rollup-plugin-html';
import polyfillsLoader from '@open-wc/rollup-plugin-polyfills-loader';

export default {
  output: {
    dir: 'dist',
  },
  plugins: [
    html({
      inputPath: 'index.html',
      inject: false,
    }),
    polyfillsLoader({
      htmlFileName: 'index.html',
      polyfills: {
        coreJs: true,
        fetch: true,
        webcomponents: true,
      },
    }),
  ],
};
```

### Multiple rollup build outputs

```js
import html from '@open-wc/rollup-plugin-html';
import polyfillsLoader from '@open-wc/rollup-plugin-polyfills-loader';

const htmlPlugin = html({
  inputPath: 'demo/single-build/index.html',
  inject: false,
});

export default {
  output: [
    {
      format: 'system',
      dir: './demo/dist/legacy',
      plugins: [htmlPlugin.addOutput('legacy')],
    },
    {
      format: 'es',
      dir: './demo/dist',
      plugins: [htmlPlugin.addOutput('modern')],
    },
  ],
  plugins: [
    htmlPlugin,
    polyfillsLoader({
      htmlFileName: 'index.html',
      modernOutput: 'modern',
      legacyOutput: { name: 'legacy', test: "!('noModule' in HTMLScriptElement.prototype)" },
      polyfills: {
        coreJs: true,
        fetch: true,
        webcomponents: true,
      },
    }),
  ],
};
```

### Multi page build

```js
import html from '@open-wc/rollup-plugin-html';
import polyfillsLoader from '@open-wc/rollup-plugin-polyfills-loader';

export default {
  output: {
    dir: './demo/dist',
  },
  plugins: [
    html({
      inputPath: './demo/multi-page/index.html',
    }),
    polyfillsLoader({
      htmlFileName: 'index.html',
      polyfills: { coreJs: true, fetch: true },
    }),

    html({
      inputPath: './demo/multi-page/pages/page-a.html',
      name: 'pages/page-a.html',
    }),
    polyfillsLoader({
      htmlFileName: 'pages/page-a.html',
      polyfills: { coreJs: true, fetch: true },
    }),

    html({
      inputPath: './demo/multi-page/pages/page-b.html',
      name: 'pages/page-b.html',
    }),
    polyfillsLoader({
      htmlFileName: 'pages/page-b.html',
      polyfills: { coreJs: true, fetch: true },
    }),

    html({
      inputPath: './demo/multi-page/pages/page-c.html',
      name: 'pages/page-c.html',
    }),
    polyfillsLoader({
      htmlFileName: 'pages/page-c.html',
      polyfills: { coreJs: true, fetch: true },
    }),
  ],
};
```

You can make this shorter with a helper function:

```js
import html from '@open-wc/rollup-plugin-html';
import polyfillsLoader from '@open-wc/rollup-plugin-polyfills-loader';

function createPage(inputPath, name) {
  return [
    html({ inputPath, name }),
    polyfillsLoader({
      htmlFileName: name,
      polyfills: { coreJs: true, fetch: true },
    }),
  ];
}

export default {
  output: {
    dir: './demo/dist',
  },
  plugins: [
    ...createPage('./demo/multi-page/index.html', 'index.html'),
    ...createPage('./demo/multi-page/pages/page-a.html', 'pages/page-a.html'),
    ...createPage('./demo/multi-page/pages/page-b.html', 'pages/page-b.html'),
    ...createPage('./demo/multi-page/pages/page-c.html', 'pages/page-c.html'),
  ],
};
```
