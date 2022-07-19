# Preset

持续集成常用开发配置

## Prettier

**dev：**
- `prettier`：格式化所有文件
- `prettier-plugin-organize-imports`：按引用深度 排序 合并 删除没有使用的 `imports` 声明
- `@volar/vue-typescript`：上述插件对于单个 `vue` 文件的拓展
- `prettier-plugin-packagejson`：合理化排序 `package.json` 的 key

## Editor

**file：**
- `.editorconfig`：告知 ide 相关规则

## Typescript

**file：**
- `tsconfig.json`：配置 ts 规则

**dev：**
- `@types/react`：`react` 一套
- `@types/react-dom`：同上
