# csharp-wasm-hello

Hello World with C# + WebAssembly (Blazor WebAssembly), deployed to GitHub Pages.

**https://bubbleshaker.github.io/csharp-wasm-hello/**

The page ships no application JavaScript. The C# in `src/HelloWasm/App.razor` is
compiled to WebAssembly and executed by the .NET runtime in the browser, which
renders the DOM and handles the button click.

## Layout

```
src/HelloWasm/
  HelloWasm.csproj      Microsoft.NET.Sdk.BlazorWebAssembly, net8.0
  Program.cs            host builder, mounts App into #app
  App.razor             the page itself (markup + C# in @code)
  _Imports.razor        shared @using directives
  wwwroot/
    index.html          host page, loads _framework/blazor.webassembly.js
    css/app.css         styling, light and dark
.github/workflows/deploy.yml
```

## Run locally

Requires the [.NET 8 SDK](https://dotnet.microsoft.com/download).

```bash
dotnet run --project src/HelloWasm
```

Then open the URL it prints (usually <http://localhost:5000>).

## Deployment

Pushing to `main` triggers `.github/workflows/deploy.yml`, which publishes the
project and uploads `dist/wwwroot` to GitHub Pages.

Two details matter for a Pages *project* site:

- **`<base href>`** — the site is served from `/csharp-wasm-hello/`, not `/`, so
  the workflow rewrites the `<base href="/" />` in `index.html` after publish.
  The committed value stays `/` so local runs work unchanged.
- **`.nojekyll`** — Blazor emits `_framework/`, and Jekyll strips paths starting
  with an underscore. The workflow writes `.nojekyll` to stop that.

### One-time repository setting

Pages must be turned on by hand once, under
**Settings -> Pages -> Build and deployment -> Source: GitHub Actions**.

The workflow cannot do this for you. `actions/configure-pages` has an
`enablement` option, but creating a Pages site is not something the workflow's
`GITHUB_TOKEN` is allowed to do (`Resource not accessible by integration`), so
the step was removed rather than left in as a guaranteed failure. Until the
setting is flipped, the `build` job passes and the `deploy` job fails.
