## Node Red Concepts
Conceitos básicos de usar raspberry com node red.
Com este software conseguimos usar toggle(acender e apagar), pisca intermitente, acender e apagar após alguns segundos, acionar e acender após alguns segundos, também é possível fazer por requisições http

### Feature
- Node red possui três variáveis que ficam alocadas em memórias, flow, context e global
- Flow, compartilhada no fluxo como todo conseguimos recuperar e setar novas dentro do mesmo fluxo
- Contexto maneira de guardar informação para compartilhar em diferentes nodes sem que a mensagem seja passado pelo fluxo, normalmente usamos o settings.js para configurar
- Global e compartilhada entre todos os fluxos, inclusive nodes, posso setar e recuperar os valores
- Exemplo abaixo para realizar um pisca intermitente foi o uso do flow, caso ele não exista na memória precisamos sempre retornar um valor false, não funciona com true


```javascript

msg.payload = flow.get("led")||false
msg.payload = !msg.payload
flow.set("led",msg.payload)
return msg;


```


##

- Para usarmos  http response, precisa ser um no do tipo Http In , se não ira acusar erro que não conseguiu inserir os headers
- Conseguimos recuperar toda informação via msg, no caso eu usei parâmetro na requisição com valor stats
- Para saber a sintaxe correta pode usar um debug no msg

```javascript
// o complemento da url /light/:stats 


let params = msg.req.params.stats

if(+params === 0 || +params === 1) { 
    msg.topic =  "Success"
    msg.payload = +params
    return msg
}

msg.topic = "Must be between 0 and 1"


return msg;

```

##
- Quando desejo trabalhar com [mensagens](https://nodered.org/docs/user-guide/messages) assíncronas posso usar a palavra reservada node
- Neste fluxo eu separei o node para enviar mensagem a saída do raspbery , motivo e que http response não aceita mensagem de funções apenas do no de http in
- Entao fiz o fluxo de envia do node separado,como  exemplo abaixo


```javascript

 // http in --> function --> template --> http response
                       |
//                     | --> function --> gpio out
  


//no bloco abaixo do function na mesma linha do gpio out


function clear() {
   clearInterval(interval)
}

let interval = setInterval(function(){
  msg.payload = !msg.payload
  node.send(msg)
  clear()
  node.done()
},msg.delay)

return msg;

```



