---
layout: article
title: Consumindo API, utilizando RxJS Observable e Angular
date: 2020-05-01 18:00:00-0300
coverPhoto: https://miro.medium.com/max/2560/1*kh36dX-r9FGmTYhVzC44HQ.png
---

Esse artigo tem como objetivo, te ensinar a consumir API, usando o framework <a href="https://angular.io/" target="_blank">Angular</a> e a biblioteca <a href="https://rxjs-dev.firebaseapp.com/" target="_blank">Rxjs</a>, de um modo simples, rápido e direto ao ponto.

![](https://miro.medium.com/max/2560/1*kh36dX-r9FGmTYhVzC44HQ.png)

## 1. Instalação

Vamos utilizar a plataforma <a href="https://code.visualstudio.com/" target="_blank">Visual Studio Code</a>, recomendo utilizar a mesma.

Agora vamos baixar o <a href="https://nodejs.org/en/" target="_blank">Node.js.</a>

Instale o <a href="https://cli.angular.io/" target="_blank">Angular CLI.</a>
> npm install -g @angular/cli 

Criando o projeto

> ng new crud-observable<br>
> ng cd crud-observable<br>
> ng serve<br>

O RxJS é instalado automaticamente.

Para abrirmos o projeto no Visual Studio Code, basta ir na raiz do projeto e digitar seguinte comando:

> code .

## 2. Criando componente, serviço e interface

Em primeiro lugar, vamos acessar a pasta onde iremos criar os arquivos necessários
> cd src/app/

Em segundo, criaremos o nosso componente 

> ng generate component post

Esse é o componente que iremos usar para consumir os serviços<br>

Em terceiro, nossa interface

> ng g interface resource/post resource

Essa interface servirá para tiparmos o retorno do observable

Em quarto, o serviço 

> ng generate service services/post

Esse serviço é onde nós usaremos o observable para consumir a API

## 3. Criando nossa Interface

Nossa interface, deve ser feita, de  acordo com os dados que vamos trabalhar.<br>
No typeScript, podemos tipar nossas variáveis. Sendo assim criando um contrato, que deve ser seguido.

<pre class="pure">
            export interface Post {
                userId: number;
                id?: number;
                title: string;
                body: string;
            }        
</pre>

essas variáveis, foram criadas a partir da nossa <a href="https://jsonplaceholder.typicode.com/posts" target="_blank">API fake</a><br>
Observe que na variável $id, temos um "?", isso significa que a variável não é obrigatorio, você vai saber o motivo mais na frente

## 4. Criando os serviços

Vamos utilizar o httpClient para fazer as requisições via http<br> 
Em nosso app.module.ts, vamos importar o HttpClienteModule

<pre class="pure">
            import { HttpClientModule } from '@angular/common/http';
            imports: [ HttpClientModule ]      
</pre>

Agora estamos pronto para usar o httpClient<br>
No arquivo post.service.ts, vamos fazer a injeção dele, em nosso servico

<pre class="pure">
            import { HttpClient } from '@angular/common/http';<br>
            constructor(private http: HttpClient) { }     
</pre>

Criaremos a constante API_BASE logo abaixo dos imports<br>
Usuaremos uma API fake

<pre class="pure">
            const API_BASE = 'https://jsonplaceholder.typicode.com';    
</pre>

Ela tem a nossa URI base<br>

Agora ja podemos fazer nossa primeira requisição via http<br>

## 4.1 Result()

Criaremos um método para mapear nosso retono

<pre class="pure">
            result(): any{
                return pipe(
                map((res: any) => res),
                catchError((err: any) => throwError({ message: err })))
            } 
</pre>

 Esse método é bem simples, onde temos o retono com map, se tudo ocorrer como o esperado. Caso ocorra um erro ele retona a mensagem.

## 4.2 GET()

<pre class="pure">
            get(): Observable< Post[] >{
                return this.http.get<Post[]>(`${ API_BASE }/posts`)
                    .pipe(this.result());
            }
</pre>

Na primeira linha, estamos criando nosso método get(), com um retono do tipo observable, com tipo post[]. O quer significa que nosso observable espera uma lista de post<br>
Na segunda, é nosso retono, estamos usando o http(que injetamos em nosso contrutor) http.get(), que é o tipo de requisição que será feita. Dentro do parenteses, estamos passando a URI, que retonara nosso arrays de post<br>
Depois só chamamos o metodo result() para retonar o resultado

## 4.3 POST()

<pre class="pure">
        post(post: Post): Observable< Post > {
            return this.http.post(`${ API_BASE }/posts`, post)
                .pipe(this.result());
        }
</pre>

Na primeira linha, estamos criando nosso método post. O observable espera o post que foi criado<br>
Na segunda, é nosso retono. http.post(), que é o tipo de requisição que será feita. Podemos perceber que estamos usando a mesma rota, porém nosso http agora é post(), e não o get(). e como um post precisa de um body, estamos passando o $post que desejamos criar<br>
Depois chamamos o result()

## 4.4 PUT()

<pre class="pure">
        put(post: Post): Observable< Post > {
            return this.http.put(`${ API_BASE }/posts/${ post.id }`, post)
                .pipe(this.result());
        }
</pre>

Na primeira linha, estamos criando nosso método put(), e como o post(), o observable espera o post que foi atualizado<br>
Na segunda, é nosso retono. Agora estamos usando o http.put(). Agora acrescentou em nossa rota um $id, que identificará o post a ser atualizado, e em seguida passamos o body, que é as novas informações do post<br>
E logo em seguinda o result()

## 4.5 DELETE()

<pre class="pure">
        delete(id: number): Observable< Object > {
            return this.http.delete(`${ API_BASE }/posts/${ id }`);
        }
</pre>

Na primeira linha, estamos criando nosso método delete(), e retona um observable do tipo Object.<br>
Na segunda, é nosso retono. Agora estamos usando o http.delete(). Como o put(), passamos em nossa rota um $id. Nese caso identificará o post a ser deletado<br>

## 5 Criando os métodos para realizar o CRUD

Em nosso app.module.ts, caso o componente não tenha sido declarado, vamos declarar

<pre class="pure">
            import { PostComponent } from './post/post.component';
            declarations: [PostComponent]     
</pre>

No mesmo arquivos, vamos fornecer nosso serviço

<pre class="pure">
            import { PostService } from './services/post.service';
            providers: [PostService],   
</pre>

Com isso, agora podemos acessar nosso serviço

No arquivo post.componente.ts, vamos fazer a injeção do nosso serviço

<pre class="pure">
            import { PostService } from './../services/post.service';
            constructor(private postService: PostService) { }   
</pre>

Dentro da class, vamos criar as seguintes variáveis

<pre class="pure">
            lista: Post[];
            form: Post = {userId: 1, title: 'Observable', body: 'Angular e RxJs'};
            formUpdate: Post = {id: 1, userId: 1, title: 'Observable', body: 'Angular e RxJs'};
            formId: number = 10;
</pre>

A variável $lista vai receber uma lista de post
O $form, esta sendo populado, pois será ele o que iremos salvar, usando o post. Observe que não estamos declarando o id, pois ele será gerado automaticamente pelo back-end 
O $formUpdate é contem todos os elementos da minha interface, inclusive o $id, pois será a partir dele que o back-end saberá qual registro será atualizado
O &formId é do tipo number, e estar recebendo um número, referente ao id que usaremos para deletar um registro

## 5.1 Criando o metodo get()

<pre class="pure">
            this.postService.get().subscribe((res) => {
                this.lista = res
                console.log(this.lista);
            });
</pre>

O método esta chamndo o serviço $postService, e nele estamos chamndo o metodo get(), onde ele retonar um observable<br>
Com isso podemos usar o subscribe para se inscrever no observable.<br>
o $res é a variavel que tem o retono do método e estamos atribuindo ela a nossa variável $lista e depois imprimindo o resultado;

## 5.2 Criando o metodo post()

<pre class="pure">
            post(){
                this.postService.post(this.form).subscribe((res) => console.log(res));
            }
</pre>

O método está chamndo o post()<br>
Estamos passando a variável $form como parâmetro. Observe que ela é do tipo Post, e tem as informações necessárias para criar um post<br>
E estamos executando um console.log(res) para imprimir o post criado

## 5.3 Criando o metodo put()

<pre class="pure">
            put(){
                this.postService.put(this.formUpdate).subscribe((res) => console.log(res));
            }
</pre>

O serviço está chamndo o put()<br>
Estamos passando a variável $formUpdate como parâmetro. Observe que ela é do tipo Post, e tem as informações necessárias para atualizar um registro, pois diferente do post, agora temos um $id<br>
E estamos imprimindo o post atualizado

## 5.4 Criando o metodo delete()

<pre class="pure">
            delete(){
                this.postService.delete(this.formId).subscribe();
            }
</pre>

O por último, o delete()<br>
Passamos a variável $formId como parâmetro. E nela estar sendo enviado um numero refernte ao id, que utilizaremos para deletar o registro<br>

## 6. Código completo()

Aqui vou disponibilizar os arquivos post.service.ts, post.component.ts epost.resource.ts

## 6.1 post.service.ts

<pre class="pure">
            import { Post } from './../resource/post.resource';
            import { Injectable } from '@angular/core';
            import { HttpClient } from '@angular/common/http';
            import { Observable, throwError, pipe } from 'rxjs';
            import { catchError, map } from 'rxjs/operators';

            const API_BASE = 'https://jsonplaceholder.typicode.com'; 
            @Injectable({
            providedIn: 'root'
            })
            export class PostService {

                constructor(private http: HttpClient) { }

                get(): Observable< Post[] >{
                    return this.http.get< Post[] >(`${ API_BASE }/posts`)
                    .pipe(this.result());
                }

                post(post: Post): Observable< Post > {
                    return this.http.post(`${ API_BASE }/posts`, post)
                    .pipe(this.result());
                }

                put(post: Post): Observable< Post > {
                    return this.http.put(`${ API_BASE }/posts/${ post.id }`, post)
                    .pipe(this.result());
                }

                delete(id: number): Observable< Object > {
                    return this.http.delete(`${ API_BASE }/posts/${ id }`);
                }

                result(): any{
                    return pipe(
                    map((res: any) => res),
                    catchError((err: any) => throwError({ message: err })))
                }
            }       
</pre>

## 6.2 post.component.ts

<pre class="pure">
            import { Post } from './../resource/post.resource';
            import { PostService } from './../services/post.service';
            import { Component, OnInit } from '@angular/core';

            @Component({
            selector: 'app-post',
            templateUrl: './post.component.html',
            styleUrls: ['./post.component.css']
            })
            export class PostComponent implements OnInit {

                lista: Post[];

                form: Post = {userId: 1, title: 'Observable', body: 'Angular e RxJs'};

                formUpdate: Post = {id: 1, userId: 1, title: 'Observable update', <br>
                body: 'Angular e RxJs update'};

                formId: number = 10;

                constructor(private postService: PostService) { }

                ngOnInit() {}

                get(){
                    this.postService.get().subscribe((res) => {
                    this.lista = res
                    console.log(this.lista);
                    });
                }

                post(){
                    this.postService.post(this.form).subscribe((res) => console.log(res));
                }

                put(){
                    this.postService.put(this.formUpdate).subscribe((res) => console.log(res));
                }

                delete(){
                    this.postService.delete(this.formId).subscribe();
                }
            }

</pre>

## 6.3 post.resource.ts

<pre class="pure">
            export interface Post {
                userId: number;
                id?: number;
                title: string;
                body: string;
            }
</pre>

O projeto completo está no <a href="https://github.com/Leonardo1065a/crud-observable" target="_blank">GitHub</a>
