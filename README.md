# swc path alias bug

Steps to reproduce:
* Install [volta](https://volta.sh/) (or set the node version in the way you wish)
* Run `npm install && npx swc ./src/module-2.ts > output.js`

You will see:
```js
// ❌ 👇 Should be ./module-1/some/deep/add
import add from "./module-1/some/deep/@alias-one";
function main() {
  console.log(add(1, 2));
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
function main() {
    console.log((0, _alias_one_1.default)(1, 2));
}
main();
//# sourceMappingURL=module-2.js.map
```
