# Microfrontend

## Criação de um workspace vazio
ng new microfrontend --create-application false

## Adicionando aplicações Angular (Shell e Remotes)
### Aplicação Shell
ng generate application shell --routing=true --style=scss

### Aplicação Remote Home
ng generate application home --routing=true --style=scss

### Aplicação Remote About
ng generate application about --routing=true --style=scss

### Configuração Module Federation Shell
ng add @angular-architects/module-federation --project shell --port 5000

### Configuração Module Federation Home
ng add @angular-architects/module-federation --project home --port 3000

### Configuração Module Federation About
ng add @angular-architects/module-federation --project about --port 4000

### Adicionar configuração em Shell
Shell App - webpack.config.js
```
...
remotes: {
  "home": "http://localhost:3000/remoteEntry.js",
  "about": "http://localhost:4000/remoteEntry.js",
},
...
```

### Adicionar configuração em remotes - Expondo módulo
Home/About App - webpack.config.js
Exemplo home:
```
...
name: "home",
filename: "remoteEntry.js",
exposes: {
    './Module': './projects/home/src/app/home/home.module.ts',
},
...
```

### Configurar Shell app routing
```
const routes: Routes = [
  {
    path: 'home',
    loadChildren: () => import('home/Module').then((m) => m.HomeModule)
  },{
    path: 'about',
    loadChildren: () => import('about/Module').then((m) => m.AboutModule)
  },
];
```

### Criar arquivo decl
'home/Module' e 'about/Module' não existem e o tsc irá reclamar.
Criar arquivo no shell/src/decl.d.ts com a definição:
```
declare module 'home/Module';
declare module 'about/Module';
```

## Run
Precisa executar todos os projetos. Para isso, foi adicionado ao package.json a opção npm run run:all

## Anotações importantes
A importação de módulo não pode ser Routes.forRoot. Erro de duplicidade. É necessário criar módulo dentro do remote e expô-lo.


