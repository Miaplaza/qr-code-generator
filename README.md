# QR Code Generator

A static site which allows generating QR codes from URL link with a picture of Mia in the center. Shows the result on screen and allows downloading it in one of 4 formats: SVG, PNG, JPEG and WEBP.

The project is built on `Vue 3` with `TypeScript` and uses `Bootstrap 5` for UI. It is hosted as static site on GitHub Pages by the link: https://miaplaza.github.io/qr-code-generator/.

## Recommended IDE Setup

[VSCode](https://code.visualstudio.com/) + [Vue](https://marketplace.visualstudio.com/items?itemName=Vue.volar) (and disable Vetur).

## Type Support for `.vue` Imports in TS

TypeScript cannot handle type information for `.vue` imports by default, so we replace the `tsc` CLI with `vue-tsc` for type checking. In editors, we need [Vue](https://marketplace.visualstudio.com/items?itemName=Vue.volar) to make the TypeScript language service aware of `.vue` types.

## Customize configuration

See [Vite Configuration Reference](https://vitejs.dev/config/).

## Project Setup

```sh
npm install
```

### Compile and Hot-Reload for Development

```sh
npm run dev
```

### Type-Check, Compile and Minify for Production

```sh
npm run build
```

### Lint with [ESLint](https://eslint.org/)

```sh
npm run lint
```

## Making changes & deploy

There is `dev` branch for development. All ready changes should go to `main`. The `main` branch is protected from direct pushes, so to add changes there you need to create a Pull Request from `dev` and merge it.

Deployment to GitHub Pages works using GitHub Actions configuration (see `.github/workflows/deploy.yml` file). Deploy is triggered by push to the `main` branch, so every successful merge with Pull Request triggers automatic deploy to GitHub Pages.
