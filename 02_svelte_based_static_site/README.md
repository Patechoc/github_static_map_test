# Step 1: Set Up the Svelte Project

make sure you have Node.js installed. Then, create a new Svelte project using Vite.

```shell
npm create vite@latest my-svelte-map --template svelte

Need to install the following packages:
create-vite@5.4.0
Ok to proceed? (y) y
npm WARN EBADENGINE Unsupported engine {
npm WARN EBADENGINE   package: 'create-vite@5.4.0',
npm WARN EBADENGINE   required: { node: '^18.0.0 || >=20.0.0' },
npm WARN EBADENGINE   current: { node: 'v14.21.3', npm: '9.8.1' }
npm WARN EBADENGINE }
✔ Select a framework: › Svelte
✔ Select a variant: › TypeScript

Scaffolding project in /home/patrick/dev/RS/github_static_map_test/02_svelte_based_static_site/my-svelte-map...

Done. Now run:

  cd my-svelte-map
  npm install
  npm run dev
```

```shell
cd my-svelte-map
npm install
```

# Step 2: Install Leaflet and Other Dependencies
Install Leaflet and Svelte components for Leaflet.

```shell
npm install leaflet svelte-leaflet
```

# Step 3: Create the Svelte Component for the Map

Create a new Svelte component named Map.svelte in the src directory.    

```shell
mkdir src
touch src/Map.svelte
```

# Step 4: Update the App.svelte

Update the App.svelte file to use the Map component.

```shell
touch src/App.svelte