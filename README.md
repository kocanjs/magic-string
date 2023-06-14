This library aims to provide codegen helpers and data structure for Kdu language plugin API v1.x that does not depend on Kocan runtime.

## Usage

```html
<script>problems = 99</script>

<more-script lang="js">console.log( answer )</more-script>
```

```ts
import {
  toString,
  overwrite, // not yet support
} from '@nkduy/magic-string';

/** @type {import('@kocan/kdu-language-core').KduLanguagePlugin} */
const plugin = () => {
	return {
		name: 'example-kdu-language-plugin',
		version: 1,
		resolveEmbeddedFile(fileName, sfc, embeddedFile) {
			if (embeddedFile.fileName.replace(fileName, '').match(/^\.(js|ts|jsx|tsx)$/)) {

        const s = embeddedFile.content;
        toString(s); // 'problems = 99'

        overwrite(s, 0, 8, 'answer'); // not yet support
        toString(s); // 'answer = 99'

        overwrite(s, 11, 13, '42'); // not yet support
        toString(s); // 'answer = 42'

        // add string by Array method directly
        s.unshift('var ');
        s.push(';');
        toString(s); // 'var answer = 42;'

        for (const block of sfc.customBlocks) {
          if (block.type === 'more-script') {
            s.push([
              block.content, // text to add
              block.name, // source
              0, // content offset in source
              {
                 // langauge capabilities to enable in this segment
                hover: true,
                references: true,
                definition: true,
                diagnostic: true,
                rename: true,
                completion: true,
                semanticTokens: true,
              },
            ]);
            toString(s); // 'var answer = 42;console.log( answer )'
          }
        }
      }
    }
  };
};
module.exports = plugin;
```
