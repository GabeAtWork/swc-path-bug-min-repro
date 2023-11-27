# swc path alias bug

Steps to reproduce:
* Install [volta](https://volta.sh/) (or set the node version in the way you wish)
* Run `npm install && npx swc ./src/module-2.ts > output.js`

You will see:
```js
// ❌ 👇 Should be ./module-1/some/deep/add
import add from "./module-1/some/deep/@alias-one";
// ✅ 👇 This one is correct though, because of the presence of an index file
import multiply from "./module-1/some/deep/multiply";
function main() {
    console.log(add(1, 2));
    console.log(multiply(1, 2));
}
main();
```

For contrast, running `npx tsc --project ./tsconfig.json && npx tsc-alias` results in:

```js
// In dist/module-2.js
"use strict";
var __importDefault = (this && this.__importDefault) || function (mod) {
    return (mod && mod.__esModule) ? mod : { "default": mod };
};
Object.defineProperty(exports, "__esModule", { value: true });
// ✅ Aliasing worked correctly
const _alias_one_1 = __importDefault(require("./module-1/some/deep/add"));
const _alias_two_1 = __importDefault(require("./module-1/some/deep/multiply"));
function main() {
    console.log((0, _alias_one_1.default)(1, 2));
    console.log((0, _alias_two_1.default)(1, 2));
}
main();
//# sourceMappingURL=module-2.js.map
```

It seems that the bug only occurs when the path ends with a file name (here, `./src/module-1/some/deep/add`).
When there is an `index.ts` file, the aliasing works as intended (see `./src/module-1/some/deep/multiply`).
