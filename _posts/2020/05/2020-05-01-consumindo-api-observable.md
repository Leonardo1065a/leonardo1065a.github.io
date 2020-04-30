---
layout: article
title: Consumindo API, utilizando RxJS Observable e Angular
date: 2020-05-01 18:00:00-0300
coverPhoto: https://miro.medium.com/max/2560/1*kh36dX-r9FGmTYhVzC44HQ.png
---

Esse artigo tem como objetivo, te ensinar a consumir API, usando o framework <a href="https://angular.io/" target="_blank">Angular</a> e a biblioteca <a href="https://rxjs-dev.firebaseapp.com/" target="_blank">Rxjs</a>, de um modo simples, rápido e direto ao ponto.

![](https://miro.medium.com/max/2560/1*kh36dX-r9FGmTYhVzC44HQ.png)

## 1. Instalação

Vou utilizar a plataforma <a href="https://code.visualstudio.com/" target="_blank">Visual Studio Code</a>, recomendo utilizar a mesma.

Comece pelo <a href="https://nodejs.org/en/" target="_blank">Node.js.</a>

Instale o <a href="https://cli.angular.io/" target="_blank">Angular CLI.</a>
> npm install -g @angular/cli 

Criando o projeto

> ng new crud-observable
> ng cd crud-observable
> ng serve

O RxJS já é instalado automaticamente.

Para abrir o projeto no Visual Studio Code, basta ir na raiz do projeto e digitar o comando seguinte
> code .

## 2. Criando o Componente e Interface

> cd src/app/
> ng generate component usuario

Esse é o componente que vamos usar para consumir os serviços

> ng g interface resource/usuario resource

Essa interface servirá para tiparmos o retorno do observable

## 3. Criando o Service

> ng generate service services/usuario

Nesse arquivo é onde iremos usuar o observable para consumir a API

## 4. Criando o serviço

Vamos utilizar o httpClient para fazer as requisições via http. Em nosso app.module.ts, vamos importar o HttpClienteModule
<pre>
    import { BrowserModule } from '@angular/platform-browser';
    import { NgModule } from '@angular/core';
    <strong>import { HttpClientModule } from '@angular/common/http';</strong>

    import { AppComponent } from './app.component';

    @NgModule({
    declarations: [
        AppComponent,
    ],
    imports: [
        BrowserModule,
        <strong>HttpClientModule</strong>
    ],
    providers: [],
    bootstrap: [AppComponent]
    })
    export class AppModule { }
</pre>

Agora estamos pronto para usar o httpClient
Vamos fazer a injeção dele, em nosso servico
<pre>
    constructor(private http: HttpClient) { }
</pre>

Agora ja podemos fazer nossa primeira requisição via http

<pre>
        get(): Observable<UsuarioResource[]>{
            return this.http.get<UsuarioResource[]>(`${ API_BASE }/todos`)
            .pipe(map((res: any) => res.json() ),
                catchError((err: any) => throwError(err.json())));
  }
</pre>

I love sesame snaps croissant brownie I love. Candy canes I love I love sesame snaps fruitcake. Tart ice cream I love tootsie roll sweet roll brownie sweet roll. Marshmallow chocolate cake danish I love caramels candy canes gummi bears bonbon dragée. Biscuit tootsie roll oat cake gingerbread powder pudding. Jujubes dragée donut carrot cake muffin icing. Carrot cake wafer caramels pie apple pie sweet candy canes I love. Danish danish chocolate bar cake. Chocolate cake jujubes gummi bears lollipop topping. Sesame snaps marshmallow candy macaroon lemon drops pudding jujubes brownie.

Croissant liquorice sugar plum I love sesame snaps lollipop I love bonbon. Tiramisu macaroon tootsie roll. I love jujubes caramels cheesecake. I love chupa chups carrot cake bonbon biscuit chocolate cake oat cake cake jujubes. Jelly danish pastry tart. Powder powder pie dessert marshmallow chocolate.
