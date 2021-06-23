---
# Fotogram App


...Para iniciar [Rollup](https://rollupjs.org):

```bash
npm run dev
```

El servidor se inicia en [localhost:5000](http://localhost:5000). 


## Info del generador de pwa de svelte.

By default, the server will only respond to requests from localhost. To allow connections from other computers, edit the `sirv` commands in package.json to include the option `--host 0.0.0.0`.

## PWA Configuration

- The `service-worker.js` and `manifest.json` files are in the `public` folder.
- You should update the icons in `/public/images/icons`
- For an offline experience edit the `/public/offline.html` file.
- This PWA is installable. For more information on how to use check [this repo](https://github.com/pwa-builder/pwa-install).
  Note: If you don't want to make the app installable you can remove the script from the `index.html` file in the `public` folder.

For more info, this template was made following this [tutorial](https://codelabs.developers.google.com/codelabs/your-first-pwapp)

## Building and running in production mode

To create an optimised version of the app:

```bash
npm run build
```
