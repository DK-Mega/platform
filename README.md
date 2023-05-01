# Create a WebPack Module Federation Angular - Root
- Crie uma aplicação angular que será o servidor que irá redirecionar as rotas das outras aplicações
  
```
$ ng new shell
```

- Adicione o modulo federation

```
$ cd ./shell
$ ng add @angular-architects/module-federation
? Project name (press enter for default project) 
    shell
? Port to use
    5000
```

- Adicionaremos no Shell a configuração do webpackservice

```
** shell/webpack.config.js
new ModuleFederationPlugin({
        library: { type: "module" },     
        
        // For hosts (please adjust)
        remotes: {
          "mfe1": "http://localhost:3000/remoteEntry.js"
          // "mfe1" se refere ao nome do microfrontend
        },

        shared: share({
          "@angular/core": { singleton: true, strictVersion: true, requiredVersion: 'auto' }, 
          "@angular/common": { singleton: true, strictVersion: true, requiredVersion: 'auto' }, 
          "@angular/common/http": { singleton: true, strictVersion: true, requiredVersion: 'auto' }, 
          "@angular/router": { singleton: true, strictVersion: true, requiredVersion: 'auto' },

          ...sharedMappings.getDescriptors()
        })
        
    }),
```

- No app.routing precisamos preencher as rotas que serão utilizadas

```
** shell/src/app/app-routing.module.ts
const routes: Routes = [
  { path: "", component: HomeComponent, pathMatch: "full" },
  { path: 'mfe1', loadChildren: () => import('mfe1/Module').then(m => m.MainModule)}
];
```

- Porém, no import('mfe1/Module') aparecerá um erro, para resolver, ainda na aplicação Shell, devemos criar um arquivo decl.d.ts dentro do src e adicionar a declaração do módulo

```
** shell/src/app/decl.d.ts
declare module 'mfe1/Module';
```