# Creating a Tree-Shakeable Package
_Under Construction - last updated February 27, 2023_

Configuring a project to tree shake properly is surprisingly difficult. This page attempts to give a conceptual overview of what’s actually happening when tree shaking and how to set it up to realize some bundle size savings.

Disclaimer: This page attempts to simplify the overall process to make it easily understandable. There will be exceptions to assertions made in this document. In many cases, with additional configuration you can work around many of the limitations and requirements I talk about here. The idea of this document is to give a general idea of what a proper tree-shaking setup should look like, to get people started. It is not intended to be exhaustive or a definitive guide.

First off, let’s talk about what happens when tree shaking at a conceptual level. One team builds a package and publishes it to npm, then another team bundles it in with their page to deliver on the site. Or they import it into another package, but eventually it’s included on some page of the site. For simplicity, we’ll talk about a single package being consumed directly by a page on the site.

The general flow would look something like this:
* Package is developed
* Package is built (optional)
  * Bundler is executed and tree shaking may happen
* Package is published in npm
* Package is installed in page
* Page is built & bundled
* Bundler is executed and tree shaking may happen

Let’s zoom in on step two, the package build. This is, technically, optional. You can put whatever you want in an npm package. However, if you want to use the latest development tools, and you want it to be easily consumable, you will probably want to have a build step to make the outputs of your package look somewhat standard. This means:
* If you’re using typescript, you’ll need to compile that typescript to Javascript, and likely also generate and include your type declaration files in case any of your consumers are using typescript.
* If you’re using es6 syntax (import { foo } from ‘bar';), you need to transpile that into commonjs (require('foo')), for compatibility with older frameworks (this is still the official standard).
* If you want your package to be tree shakeable, you also need to include an esm version of your package alongside the commonjs (cjs) bundle.
* Depending on how your package is intended to be consumed, you may choose to run tree shaking and bundling at this time. This is not always optimal. In many cases, this will cause the consuming page to end up with a larger bundle than if you’d left tree shaking to be done in step 5 by the consuming page’s bundler.
  * Tree shaking at this time means your bundler will traverse your app tree starting from your entry point (index.js, usually), and everything it references, including dependencies (** by default, you can configure the dependencies to be excluded), and puts it in a bundle. However, that bundle cannot then be tree shaken again by the next pass (in step 5), tree shaking in step 2 prevents the page from excluding parts of the package it doesn’t need.

In Step 5, assuming it is configured to do so, the bundler will attempt to tree shake out any dead code. Several things must be managed properly for it to do so successfully:
* The page must include code from the package using the import { foo } from "bar" syntax. Statements like import * from "bar", while convenient, will prevent tree shaking, because you’re signaling to the bundler that you need everything in that package. require("bar") will also prevent tree shaking.
* The package must have been properly configured for tree shaking.
  * Must export an esm version of the package, with a module entry in package.json pointing to it.
  * The esm version of the package should be split into discrete chunks, not all bundled into a single file. Smaller chunks means more granular tree shaking, and therefore larger savings. Usually this means that you want to preserve the original file structure of the module in the npm package using the preserveModule setting in webpack or rollup (assuming you use one of those to build the package before publishing).
  * If it’s possible, the sideEffects field should be set in the package.json. This signals to the bundler that the package does not have any side effects (eg. just by executing the code/initializing the file it does something on the page), and it is safe to exclude the files if they’re not referenced. You must verify that your package actually does not have any side effects before setting this value to false, but when set this will allow the bundler to be much more aggressive about pruning dead code, so try to set it if you can.

## Configuration specifics
### `package.json`
Below is a trimmed-down version of the package.json, with comments calling out the importance of certain lines.

```
{
  "name": "@vp/react-bookends",
  "main": "dist/index.cjs.js", // Points to the cjs bundle
  "module": "build/index.esm.js", // Points to the esm entrypoint (ideally not a bundle)
  "typings": "dist/index.d.ts", // Points to the types declaration
  "scripts": { ... },
  "sideEffects": [ // Either set to false or an array of files that do have side effects
    "*.css" //If you are using and including scss, you may instead need "**/*.scss.js" here
  ],
  "dependencies": {
    // Move anything that might be used by another
    // package to peerDependencies instead!
  },
  "peerDependencies": {
    // Any dependencies that might be used by another
    // package should go here, so the page bundler can dedupe them
  },
  "devDependencies": { ... }
}
```

### `tsconfig.json`
In tsconfig, set the target to either es5, es2015, or es6 so tsc will output files with in their original structure instead of bundled into a cjs bundle.

### `webpack.config.js` or `rollup.config.js`
Set "preserveModules": true in the config to output files in their original structure instead of bundled as one.

### Output both a cjs file and an esm file
Make sure your build process outputs both file types, so consumers who know how to utilize it can use esm for tree shaking, but you still have cjs for backwards compatibility.
