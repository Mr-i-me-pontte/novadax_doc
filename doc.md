# Novadax API
Bem-vindo(a) à documentação API da NovaDAX
------------------------------------------

A NovaDAX disponibiliza sua documentação de API em busca de trazer uma experiência mais rápida, segura e profissional para seus clientes.

Com nossa API, você terá acesso a dados de mercado, histórico de ordens e gerenciamento de conta, de uma maneira automatizada e prática.

Realizaremos constantemente atualizações e otimizações para nosso SDK.

Antes de começar
----------------

### Criar uma conta na NovaDAX

Caso você ainda não possua uma conta na NovaDAX, crie uma agora mesmo [clicando aqui](https://www.novadax.com/).

### Gerar uma chave da API

É preciso adquirir uma chave de acesso (também chamada de AccessKey) e uma chave segredo (também chamada de SecretKey) para poder autenticar as requisições. Depois do login, encontre o símbolo do usuário no menu principal e clique em "Chave de API e Subconta" no submenu para criar chaves para API.

Os endpoint da API NovaDAX se dividem em públicos e privados.

### Endpoints públicos

Podem ser acessados sem necessidade de autenticação para obter:

*   Dados de mercado
*   Informações básicas

### Endpoints privados

Precisam ser acessados com autenticação para gerenciar:

*   Ordens
*   Conta

### Acesso a URLs

`https://api.novadax.com`

### Taxa limite de requisições

Endpoints públicos: ao máximo 60 requisições a cada segundo por IP;

Endpoints privados: até 20 requisições por segundo para cada API AccessKey.

### Autenticação

A fim de proteger a comunicação da API contra alterações não autorizadas, todas as chamadas de endpoints privados precisam ser autenticadas com sua API AccessKey.

Estrutura das requisições válidas

*   É necessário fazer uma chamada para a URL `api.novadax.com`, por exemplo, `https://api.novadax.com/v1/common/symbols`.
    
*   AccessKeyId: sua API `AccessKey`
    
*   Signature Method: utilize `HMAC-SHA256` hash
    
*   Timestamp: a hora atual do sistema em milissegundos, por exemplo, 1564988445199.
    

Parâmetros obrigatórios e opcionais: existem parâmetros obrigatórios e opcionais para acessar cada endpoint. Você pode verificar o significado desses parâmetros na descrição de cada endpoint.

Signature: mensagem criptografada para prevenir transportes e alterações não autorizadas.

### Método de Autenticação

#### Passos para autenticar uma requisição GET

#### 1\. Query string é

`queryStr = "name=joao&cpf=123456&birthday=2017-08-01"`

#### 2\. Codifique a query string de consulta e ordene parâmetros seguindo a ordem ASCII.

`sort(urlencode(queryStr)) // birthday=2017-08-01&cpf=123456&name=joao`

#### 3\. Transforme a string com formato `{método de requisição}\n{caminho de requisição}\n{string a ser codificada}\n{timeStamp}`

`signStr = GET\n/v1/orders/get\nbirthday=2017-08-01&cpf=123456&name=joao\n1564988445199`

#### 4\. Utilize a string gerada no passo 3 e sua "SecretKey" para autenticar sua requisição

`sign = hmac_sha256(signStr,SecretKey)`

`request.header.add("X-Nova-Access-Key",AccessKey)`

`request.header.add("X-Nova-Signature",Sign)`

`request.header.add("X-Nova-Timestamp",TimeStamp)`

#### 6\. Exemplo de autenticação GET em Java

![Tux, the Linux mascot](https://doc.novadax.com/pt-BR/images/get_encrypt.png)

#### Passos para autenticar uma requisição POST

#### 1\. O corpo da requisição (request body) é

`body = {"name":"joao","cpf":"123456","birthday":"2017-08-01"} // MD5 value is 7d8f374d786079cfade9d1c2a358137c`

#### 2\. Transforme o "corpo da requisição" com o algoritmo MD5, seguindo o formato `%s\n%s\n%s\n%s`

`signStr = POST\n/v1/order/create\n7d8f374d786079cfade9d1c2a358137c\n1564988445199`

#### 3\. Utilize a string gerada no passo 2 e sua "SecretKey" para autenticar sua requisição

`sign = hmac_sha256(signStr,SecretKey)`

`request.header.add("X-Nova-Access-Key",AccessKey)`

`request.header.add("X-Nova-Signature",Sign)`

`request.header.add("X-Nova-Timestamp",TimeStamp)`

#### 5\. Exemplo de autenticação POST em Java

![Tux, the Linux mascot](https://doc.novadax.com/pt-BR/images/post_encrypt.png)

### Estrutura de resposta (response)

A resposta será retornada no formato JSON. O superior de reposta em JSON contém três meta dados que são "code", "data" e "message". A sua resposta correspondente por API está no campo "data".

> Estrutura de resposta de sucesso

```
{
    "code": "A10000",
    "data": {...},
    "message": "Success"
}


```


> Estrutura de resposta de erro

```
{
    "code": "A99999",
    "data": {...},
    "message": "fail."
}


```


Formato de resposta:


|Campo  |Tipo de|Descrição                             |
|-------|-------|--------------------------------------|
|code   |string |O status retornado por endpoint       |
|message|string |A resposta simplificada               |
|data   |object |A resposta detalhada se for disponível|


### Tabela de códigos de status



* Código: A99999
  * Código de status HTTP: 500
  * Mensagem: Failed
  * Descrição: Erro interno
* Código: A10000
  * Código de status HTTP: 200
  * Mensagem: Success
  * Descrição: Sucesso
* Código: A10001
  * Código de status HTTP: 400
  * Mensagem: Params error
  * Descrição: Parâmetro inválido
* Código: A10002
  * Código de status HTTP: 404
  * Mensagem: Api not found
  * Descrição: Endpoint não encontrado
* Código: A10003
  * Código de status HTTP: 403
  * Mensagem: Authentication failed
  * Descrição: Falha na autenticação
* Código: A10004
  * Código de status HTTP: 429
  * Mensagem: Too many requests
  * Descrição: Excedeu o limite de requisições
* Código: A10005
  * Código de status HTTP: 403
  * Mensagem: Kyc required
  * Descrição: É preciso completar verificação de conta primeiro
* Código: A10006
  * Código de status HTTP: 403
  * Mensagem: Customer canceled
  * Descrição: Conta cancelada
* Código: A10007
  * Código de status HTTP: 400
  * Mensagem: Account not exist
  * Descrição: Subconta não existe
* Código: A10011
  * Código de status HTTP: 400
  * Mensagem: Symbol not exist
  * Descrição: O símbolo (par) não existe
* Código: A10012
  * Código de status HTTP: 400
  * Mensagem: Symbol not trading
  * Descrição: O símbolo (par) não está disponível para transacionar no momento
* Código: A10013
  * Código de status HTTP: 503
  * Mensagem: Symbol maintain
  * Descrição: O símbolo (par) está em manutenção
* Código: A30001
  * Código de status HTTP: 400
  * Mensagem: Order not found
  * Descrição: A ordem não foi encontrada
* Código: A30002
  * Código de status HTTP: 400
  * Mensagem: Order amount is too small
  * Descrição: A quantidade é insuficiente
* Código: A30003
  * Código de status HTTP: 400
  * Mensagem: Order amount is invalid
  * Descrição: A quantidade é inválida
* Código: A30004
  * Código de status HTTP: 400
  * Mensagem: Order value is too small
  * Descrição: O valor é insuficiente
* Código: A30005
  * Código de status HTTP: 400
  * Mensagem: Order value is invalid
  * Descrição: O valor é inválido
* Código: A30006
  * Código de status HTTP: 400
  * Mensagem: Price is invalid
  * Descrição: O preço é inválido
* Código: A30007
  * Código de status HTTP: 400
  * Mensagem: Insufficient balance
  * Descrição: O saldo é insuficiente
* Código: A30008
  * Código de status HTTP: 400
  * Mensagem: Order was closed
  * Descrição: A ordem já foi executada
* Código: A30009
  * Código de status HTTP: 400
  * Mensagem: Order canceled
  * Descrição: A ordem já foi cancelada
* Código: A30010
  * Código de status HTTP: 400
  * Mensagem: Order cancelling
  * Descrição: A ordem está sendo cancelada
* Código: A30011
  * Código de status HTTP: 400
  * Mensagem: Order price too high
  * Descrição: O preço é alto demais
* Código: A30012
  * Código de status HTTP: 400
  * Mensagem: Order price too low
  * Descrição: O preço é baixo demais


### Dúvidas Frequentes

#### Falha na autenticação

*   Certifique-se de que a API AccessKey está válida e inserida corretamente e que o IP usado é permitido para acesso.
*   Certifique-se de que o timestamp está ajustado para o horário UTC.
*   Certifique-se de que os parâmetros codificados são ordenados de acordo com a ordem ASCII.
*   Certifique-se de que o formato de codificação é `utf-8`.
    
*   Certifique-se de que a chamada GET é enviado como formulário HTTP.
    
*   Certifique-se de que a chamada POST é feita no formato JSON.
    
*   Certifique-se de que a autenticação está codificada.
    
*   Certifique-se de `Content-Type: application / json` é colocado no header da chamada POST.
    

SDK e Vídeo Tutorial
--------------------

*   [Vídeo tutorial no nosso canal de YouTube](https://youtu.be/XX4jTksUFB8)
*   [Java](https://github.com/novadaxapi)
*   [Python](https://github.com/novadaxapi)
*   Node.js
*   PHP

[Caso tenha necessidade, você pode acessar nosso SDK no GitHub.](https://github.com/novadaxapi)

Além do nosso próprio SDK, a NovaDAX foi integrada com a biblioteca CCXT. A CCXT é nosso fornecedor autorizado de SDK e você pode acessar a API da NovaDAX através da CCXT. Para mais informações, confira [https://ccxt.trade](https://ccxt.trade/).



* Data: 2019-09-18
  * Endpoint: /v1/account/withdraw/coin
  * Tipo: Adicionar
  * Detalhes de atualização: Sacar em criptomoedas
* Data: 2019-12-05
  * Endpoint: POST /v1/orders/create
  * Tipo: Novo campo
  * Detalhes de atualização: Novo campo para subconta (accountId)
* Data: 2019-12-05
  * Endpoint: POST /v1/orders/list
  * Tipo: Novo campo
  * Detalhes de atualização: Novo campo para subconta (accountId)
* Data: 2019-12-05
  * Endpoint: GET /v1/account/subs
  * Tipo: Adicionar
  * Detalhes de atualização: Lista de subcontas
* Data: 2019-12-05
  * Endpoint: GET /v1/account/subs/balance
  * Tipo: Adicionar
  * Detalhes de atualização: Saldo de subcontas
* Data: 2019-12-05
  * Endpoint: GET /v1/account/subs/transfer/record
  * Tipo: Adicionar
  * Detalhes de atualização: Histórico de transferências de subcontas
* Data: 2019-12-05
  * Endpoint: POST /v1/account/subs/transfer
  * Tipo: Adicionar
  * Detalhes de atualização: Transferência entre conta principal e subcontas
* Data: 2020-03-05
  * Endpoint: POST /v1/common/symbol
  * Tipo: Adicionar
  * Detalhes de atualização: Dados de um símbolo (par) específico
* Data: 2020-05-20
  * Endpoint: GET /v1/orders/list
  * Tipo: Alterar
  * Detalhes de atualização: o parâmetro "symbol" passa a ser opcional
* Data: 2020-10-12
  * Endpoint: GET /v1/wallet/query/deposit-withdraw
  * Tipo: Adicionar
  * Detalhes de atualização: deposit and withdraw records
* Data: 2020-10-12
  * Endpoint: GET /v1/orders/fills
  * Tipo: Alterar
  * Detalhes de atualização: Adicionar a função de pesquisar o histórico das ordens executadas
* Data: 2020-11-11
  * Endpoint: WebSocket
  * Tipo: Adicionar
  * Detalhes de atualização: Adicionar WebSocket
* Data: 2020-12-17
  * Endpoint: GET /v1/orders/get  GET /v1/orders/list
  * Tipo: Alterar
  * Detalhes de atualização: Ordens criadas via API não serão encontradas depois de duas horas após seu cancelamento.
* Data: 2021-01-30
  * Endpoint: POST  /v1/account/withdraw/coin
  * Tipo: Change
  * Detalhes de atualização: Add chainAlias to send
* Data: 2021-01-30
  * Endpoint: POST  /v1/wallet/withdraw/coin
  * Tipo: Add
  * Detalhes de atualização: Add chainAlias to send
* Data: 2021-01-30
  * Endpoint: GET /crypto/chain/{code}
  * Tipo: Add
  * Detalhes de atualização: search code chain infomation


Dados de um símbolo (par) específico
------------------------------------

Retorna regras de negociação de um símbolo (par) específico.

```
curl "https://api.novadax.com/v1/common/symbol?symbol=BTC_BRL"

```


```
from novadax import RequestClient as NovaClient

nova_client = NovaClient('your access key(optional)', 'your secret key(optional)')

result = nova_client.get_symbol("BTC_BRL")

print(result)

```


> Conteúdo de resposta

```
{
    "code": "A10000",
    "data": {
        "symbol": "BTC_BRL",
        "baseCurrency": "BTC",
        "quoteCurrency": "BRL",
        "amountPrecision": 4,
        "pricePrecision": 2,
        "valuePrecision": 4,
        "minOrderAmount": "0.001",
        "minOrderValue": "5"
    },
    "message": "Success"
}

```


### Caminho de requisição

`GET /v1/common/symbol`

### Parâmetro de requisição


|Campo |Mandatório|Tipo  |Detalhes             |
|------|----------|------|---------------------|
|symbol|true      |string|Símbolo da negociação|


### Detalhes de resposta


|Campo          |Tipo  |Detalhes                                    |
|---------------|------|--------------------------------------------|
|symbol         |string|Símbolo de negociação                       |
|baseCurrency   |string|Moeda de base em um símbolo de negociação   |
|quoteCurrency  |string|Moeda de cotação em um símbolo de negociação|
|pricePrecision |number|Precisão (casas decimais) de preço          |
|amountPrecision|number|Precisão (casas decimais) de quantidade     |
|valuePrecision |number|Precisão (casas decimais) de valor          |
|minOrderAmount |string|Quantidade mínima de ordem                  |
|minOrderValue  |string|Valor mínimo de ordem                       |


Todos os símbolos (pares)
-------------------------

Retorna todos os símbolos (pares) disponíveis para negociar na NovaDAX.

```
curl "https://api.novadax.com/v1/common/symbols"

```


```
from novadax import RequestClient as NovaClient

nova_client = NovaClient('your access key(optional)', 'your secret key(optional)')

result = nova_client.list_symbols()

print(result)

```


> Conteúdo de resposta

```
{
    "code": "A10000",
    "data": [
        {
            "symbol": "BTC_BRL",
            "baseCurrency": "BTC",
            "quoteCurrency": "BRL",
            "amountPrecision": 4,
            "pricePrecision": 2,
            "valuePrecision": 4,
            "minOrderAmount": "0.001",
            "minOrderValue": "5",
        },
        {
            "symbol": "ETH_BRL",
            "baseCurrency": "ETH",
            "quoteCurrency": "BRL",
            "amountPrecision": 4,
            "pricePrecision": 2,
            "valuePrecision": 4,
            "minOrderAmount": "0.01",
            "minOrderValue": "5"
        }
    ],
    "message": "Success"
}

```


### Caminho de requisição

`GET /v1/common/symbols`

### Parâmetro de requisição

Nenhum parâmetro é necessário para este endpoint.

### Detalhes de resposta


|Campo          |Tipo  |Detalhes                                    |
|---------------|------|--------------------------------------------|
|symbol         |string|Símbolo de negociação                       |
|baseCurrency   |string|Moeda de base em um símbolo de negociação   |
|quoteCurrency  |string|Moeda de cotação em um símbolo de negociação|
|pricePrecision |number|Precisão (casas decimais) de preço          |
|amountPrecision|number|Precisão (casas decimais) de quantidade     |
|valuePrecision |number|Precisão (casas decimais) de valor          |
|minOrderAmount |string|Quantidade mínima de ordem                  |
|minOrderValue  |string|Valor mínimo de ordem                       |


Hora atual do sistema
---------------------

Retorna a hora atual do sistema ajustado para o horário UTC em milissegundos.

```
curl "https://api.novadax.com/v1/common/timestamp"

```


```
from novadax import RequestClient as NovaClient

nova_client = NovaClient('your access key(optional)', 'your secret key(optional)')

result = nova_client.get_timestamp()

print(result)

```


> Response Content

```
{
    "code": "A10000",
    "data": 1565080348983,
    "message": "Success"
}

```


### Caminho de requisição

`GET /v1/common/timestamp`

### Parâmetro de requisição

Nenhum parâmetro é necessário para este endpoint.

### Detalhes de resposta

A hora atual do sistema ajustado para o horário UTC em milissegundos.

Obter dados de ticker de todos os pares de negociação
-----------------------------------------------------

Retorna dados de negociação para todos os pares disponíveis na NovaDAX com o resumo de informações importantes das últimas 24 horas.

```
curl 'https://api.novadax.com/v1/market/tickers'

```


```
from novadax import RequestClient as NovaClient

nova_client = NovaClient('your access key(optional)', 'your secret key(optional)')

result = nova_client.list_tickers()

print(result)

```


> Conteúdo de resposta

```
{
    "code": "A10000",
    "data": [
        {
            "ask": "34708.15",
            "baseVolume24h": "34.08241488",
            "bid": "34621.74",
            "high24h": "35079.77",
            "lastPrice": "34669.81",
            "low24h": "34330.64",
            "open24h": "34492.08",
            "quoteVolume24h": "1182480.09502814",
            "symbol": "BTC_BRL",
            "timestamp": 1571112216346
        }
    ],
    "message": "Success"
}


```


### Caminho de requisição

`GET /v1/market/tickers`

### Parâmetros de requisição

Nenhum parâmetro é necessário para este endpoint.

### Detalhes de resposta


|Campo         |Tipo  |Detalhes                                                     |
|--------------|------|-------------------------------------------------------------|
|symbol        |string|Símbolo da negociação                                        |
|lastPrice     |string|Preço unitário da última negociação                          |
|bid           |string|Maior preço de oferta de compra das últimas 24 horas         |
|ask           |string|Menor preço de oferta de venda das últimas 24 horas          |
|open24h       |string|Preço unitário de abertura de negociação das últimas 24 horas|
|high24h       |string|Maior preço unitário de negociação das últimas 24 horas      |
|low24h        |string|Menor preço unitário de negociação das últimas 24 horas      |
|baseVolume24h |string|Volume de negociação na moeda de base das últimas 24 horas   |
|quoteVolume24h|string|Volume de negociação na moeda de cotação das últimas 24 horas|
|timestamp     |number|A hora atual do sistema ajustada para o horário UTC          |


Obter dados de ticker de um par de negociação específico
--------------------------------------------------------

Retorna dados de negociação para um par específico com o resumo de informações importantes das últimas 24 horas.

```
curl 'https://api.novadax.com/v1/market/ticker?symbol=BTC_BRL'

```


```
from novadax import RequestClient as NovaClient

nova_client = NovaClient('your access key(optional)', 'your secret key(optional)')

result = nova_client.get_ticker('BTC_BRL')

print(result)

```


> Conteúdo de resposta

```
{
    "code": "A10000",
    "data": {
        "ask": "34708.15",
        "baseVolume24h": "34.08241488",
        "bid": "34621.74",
        "high24h": "35079.77",
        "lastPrice": "34669.81",
        "low24h": "34330.64",
        "open24h": "34492.08",
        "quoteVolume24h": "1182480.09502814",
        "symbol": "BTC_BRL",
        "timestamp": 1571112216346
    },
    "message": "Success"
}

```


### Caminho de requisição

`GET /v1/market/ticker`

### Parâmetros de requisição


|Campo |Mandatório|Tipo  |Detalhes             |
|------|----------|------|---------------------|
|symbol|true      |string|Símbolo da negociação|


### Detalhes de resposta


|Campo         |Tipo  |Detalhes                                                     |
|--------------|------|-------------------------------------------------------------|
|symbol        |string|Símbolo de negociação                                        |
|lastPrice     |string|Preço unitário da última negociação                          |
|bid           |string|Maior preço de oferta de compra das últimas 24 horas         |
|ask           |string|Menor preço de oferta de venda das últimas 24 horas          |
|open24h       |string|Preço unitário de abertura de negociação das últimas 24 horas|
|high24h       |string|Maior preço unitário de negociação das últimas 24 horas      |
|low24h        |string|Menor preço unitário de negociação das últimas 24 horas      |
|baseVolume24h |string|Volume de negociação na moeda de base das últimas 24 horas   |
|quoteVolume24h|string|Volume de negociação na moeda de cotação das últimas 24 horas|
|timestamp     |number|A hora atual do sistema ajustada para o horário UTC          |


Obter dados de profundidade
---------------------------

Retorna informações de livro de ofertas para um par específico.

```
curl 'https://api.novadax.com/v1/market/depth?symbol=BTC_BRL&limit=10'

```


```
from novadax import RequestClient as NovaClient

nova_client = NovaClient('your access key(optional)', 'your secret key(optional)')

result = nova_client.get_depth('BTC_BRL', limit = 10)

print(result)

```


> Conteúdo de resposta

```
{
    "code": "A10000",
    "data": {
        "asks": [
            ["43687.16", "0.5194"],
            ["43687.2", "1.3129"]
        ],
        "bids": [
            ["43657.57", "0.6135"],
            ["43657.46", "0.0559"]
        ],
        "timestamp": 1565057338020
    },
    "message": "Success"
}

```


### Caminho de requisição

`GET /v1/market/depth`

### Parâmetro de requisição


|Campo |Mandatório|Tipo  |Detalhes                                          |
|------|----------|------|--------------------------------------------------|
|symbol|true      |string|Símbolo da negociação                             |
|limit |false     |number|O número de compras e vendas a retornar; máximo 50|


### Detalhes de resposta


|Campo    |Tipo  |Detalhes                                           |
|---------|------|---------------------------------------------------|
|asks     |array |Lista de ofertas de venda                          |
|asks[][0]|string|Preço de venda                                     |
|asks[][1]|string|Quantidade de venda                                |
|bids     |array |Lista de ofertas de compra                         |
|bids[][0]|string|Preço de compra                                    |
|bids[][1]|string|Quantidade de compra                               |
|timestamp|number|A hora atual do sistema ajustada para o horário UTC|


Obter dados de execução de ordens
---------------------------------

Retorna informações das negociações realizadas recentemente.

```
curl 'https://api.novadax.com/v1/market/trades?symbol=BTC_BRL&limit=10'

```


```
from novadax import RequestClient as NovaClient

nova_client = NovaClient('your access key(optional)', 'your secret key(optional)')

result = nova_client.list_trades('BTC_BRL', limit = 10)

print(result)

```


> Response Content

```
{
    "code": "A10000",
    "data": [
        {
            "price": "43657.57",
            "amount": "1",
            "side": "SELL",
            "timestamp": 1565007823401
        },
        {
            "price": "43687.16",
            "amount": "0.071",
            "side": "BUY",
            "timestamp": 1565007198261
        }
    ],
    "message": "Success"
}

```


### Caminho de requisição

`GET /v1/market/trades`

### Parâmetro de requisição


|Campo |Mandatório|Tipo  |Detalhes                                       |
|------|----------|------|-----------------------------------------------|
|symbol|true      |string|Símbolo da negociação                          |
|limit |false     |number|O número de negociações a retornar; default 100|


### Detalhes de resposta


|Campo    |Tipo  |Detalhes                                        |
|---------|------|------------------------------------------------|
|price    |string|Preço unitário da negociação na moeda de cotação|
|amount   |string|Quantidade da negociação na moeda de base       |
|side     |string|A ponta executora da negociação (SELL ou BUY)   |
|timestamp|number|Hora em que ordem foi executada                 |


Obter dados de candlestick
--------------------------

```
curl 'https://api.novadax.com/v1/market/kline/history?symbol=BTC_BRL&from=1567619280&to=1567620000&unit=ONE_MIN'

```


```
from novadax import RequestClient as NovaClient

result = nova_client.get_kline('BTC_BRL', unit = 'ONE_WEE',from='1567619280 ', to='1567620000 ')

print(result)

```


> Response Content

```
{
    "code": "A10000",
    "data": [
        {
            "amount": 8.25709100,
            "closePrice": 62553.20,
            "count": 29,
            "highPrice": 62592.87,
            "lowPrice": 62553.20,
            "openPrice": 62554.23,
            "score": 1602501480,
            "symbol": "BTC_BRL",
            "vol": 516784.2504067500
        }
    ],
    "message": "Success"
}

```


### Parâmetro de requisição

`GET /v1/market/kline/history`

### Parâmetro de requisição



* Campo: symbol
  * Mandatório: true
  * Tipo: string
  * Detalhes: Símbolo da negociação
* Campo: unit
  * Mandatório: true
  * Tipo: string
  * Detalhes: ONE_MIN,FIVE_MIN, FIFTEEN_MIN,HALF_HOU,ONE_HOU,ONE_DAY,ONE_WEE,ONE_MON;
* Campo: from
  * Mandatório: true
  * Tipo: number
  * Detalhes: data de início Apenas 3000 históricos preservados em qualquer unidade de tempo
* Campo: to
  * Mandatório: true
  * Tipo: number
  * Detalhes: data de término   Apenas 3000 históricos preservados em qualquer unidade de tempo


### Detalhes de respots


|Campo     |Tipo  |Detalhes                            |
|----------|------|------------------------------------|
|unit      |string|Unidade de tempo de execução        |
|amount    |string|Quantidade de moedas negociadas     |
|count     |string|Quantidade de negociações concluídas|
|openPrice |string|Preço no início                     |
|closePrice|string|Preço no fim                        |
|highPrice |string|Preço maior                         |
|lowPrice  |string|Preço menor                         |
|symbol    |string|Símbolo da negociação               |
|score     |string|Marca temporal                      |
|vol       |string|Volume de negociação                |


Introdução de ordem
-------------------

#### tipo de ordem(order.type)

*   **LIMIT**: ordem limitada
*   **MARKET**: ordem a mercado
*   **STOP\_LIMIT**: A ordem será colocada em fila de espera ao preço limite só quando o preço de mercado atingir o preço de disparo.
*   **STOP\_MARKET**: A ordem será colocada em fila de espera ao preço de mercado só quando o preço de mercado atingir o preço de disparo.

#### Operador de stop ordem（order.operator）

*   **GTE**: Igual ou superior a
*   **LTE**: Igual ou inferior a

#### Direção(order.side)

*   **BUY**: Compra
*   **SELL**: Venda

#### status da ordem(order.status)

*   **SUBMITTED**: A ordem foi submetida, mas não processada, ainda não entrou na fila de execução. O status da ordem está unfinished.
*   **PROCESSING**: A ordem foi submetida e está na fila de execução. O status da ordem está unfinished.
*   **PARTIAL\_FILLED**: A ordem foi parcialmente negociada e está na fila de execução, esperando por mais negociações. O status da ordem está unfinished.
*   **PARTIAL\_CANCELED**: A ordem tinha sido parcialmente negociada e foi cancelada pelo usuário. Não está na fila de execução. O status da ordem está finished.
*   **PARTIAL\_REJECTED**: A ordem foi recusada pelo sistema depois de ter sido parcialmente negociada. Agora não está na fila de execução. O status da ordem está finished.
*   **FILLED**: A ordem foi completamente negociada. Não está na fila de execução. O status da ordem está finished.
*   **CANCELED**: A ordem foi cancelada pelo usuário antes de ser negociada. Não está na fila de execução. O status da ordem está finished.
*   **REJECTED**: A ordem foi recusada pelo sistema antes de ser negociada. Não está na fila de execução. O status da ordem está finished.
*   **CANCELING**: A ordem está sendo cancelada, mas ainda está na fila de execução. O status da ordem está unfinished.

#### Papel da ordem(order.role)

*   **MAKER**: Contribui liquidez
*   **TAKER**: Tira liquidez

Criar ordem limitada de compra
------------------------------

Abre uma ordem limitada de compra e a envia para livro de ofertas para sua execução.

```
curl -X POST \
  'https://api.novadax.com/v1/orders/create' \
  -H 'Content-Type: application/json' \
  -H 'X-Nova-Access-Key: your access key' \
  -H 'X-Nova-Signature: signature' \
  -H 'X-Nova-Timestamp: timestamp now' \
  -d '{
    "symbol": "BTC_BRL",
    "type": "LIMIT",
    "side": "BUY",
    "price": "35000.00",
    "amount": "0.001"
  }'

```


```
from novadax import RequestClient as NovaClient

nova_client = NovaClient('your access key', 'your secret key')

result = nova_client.create_order('BTC_BRL', 'LIMIT', 'BUY', price = '35000.00', amount = '0.001')

print(result)

```


> Conteúdo de resposta

```
{
    "code": "A10000",
    "data": {
        "amount": "0.001",
        "averagePrice": null,
        "filledAmount": "0",
        "filledFee": "0",
        "filledValue": "0",
        "id": "633679992971251712",
        "price": "35000",
        "side": "BUY",
        "status": "PROCESSING",
        "symbol": "BTC_BRL",
        "timestamp": 1571122683535,
        "type": "LIMIT",
        "value": "35"
    },
    "message": "Success"
}

```


### Caminho de requisição

`POST /v1/orders/create`

### Parâmetro de requisição



* Campo: symbol
  * Mandatório: true
  * Tipo: string
  * Detalhes: Símbolo da negociação, como BTC_BRL
* Campo: type
  * Mandatório: true
  * Tipo: string
  * Detalhes: Tipo de ordem, informe: LIMIT
* Campo: side
  * Mandatório: true
  * Tipo: string
  * Detalhes: A ponta executora da ordem, informe: BUY
* Campo: amount
  * Mandatório: true
  * Tipo: string
  * Detalhes: Quantidade da moeda de base, por exemplo, a quantidade de BTC na compra de BTC_BRL
* Campo: price
  * Mandatório: true
  * Tipo: string
  * Detalhes: Preço de compra
* Campo: accountId
  * Mandatório: false
  * Tipo: string
  * Detalhes: ID da subconta, se não for informado, a ordem será colocada sob conta principal


### Detalhes de resposta


|Campo       |Tipo  |Detalhes                                      |
|------------|------|----------------------------------------------|
|id          |string|ID da ordem                                   |
|symbol      |string|Símbolo da negociação                         |
|type        |string|Tipo da ordem                                 |
|side        |string|A ponta executora da ordem                    |
|price       |string|Preço unitário da ordem                       |
|averagePrice|string|Preço médio da ordem                          |
|amount      |string|Quantidade da moeda de base                   |
|filledAmount|string|Quantidade executada da moeda de base         |
|value       |string|Quantidade da moeda de cotação                |
|filledValue |string|Quantidade executada da moeda de cotação      |
|filledFee   |string|Taxa paga                                     |
|status      |string|Status da ordem(consultar introdução de ordem)|
|timestamp   |number|Hora da criação da ordem                      |


Criar ordem limitada de venda
-----------------------------

Abre uma ordem limitada de venda e a envia para livro de ofertas para sua execução.

```
curl -X POST \
  'https://api.novadax.com/v1/orders/create' \
  -H 'Content-Type: application/json' \
  -H 'X-Nova-Access-Key: your access key' \
  -H 'X-Nova-Signature: signature' \
  -H 'X-Nova-Timestamp: timestamp now' \
  -d '{
    "symbol": "BTC_BRL",
    "type": "LIMIT",
    "side": "SELL",
    "price": "35000.00",
    "amount": "0.001"
  }'

```


```
from novadax import RequestClient as NovaClient

nova_client = NovaClient('your access key', 'your secret key')

result = nova_client.create_order('BTC_BRL', 'LIMIT', 'SELL', price = '35000.00', amount = '0.001')

print(result)

```


> Conteúdo de resposta

```
{
    "code": "A10000",
    "data": {
        "amount": "0.001",
        "averagePrice": null,
        "filledAmount": "0",
        "filledFee": "0",
        "filledValue": "0",
        "id": "633680805433102336",
        "price": "35000",
        "side": "SELL",
        "status": "PROCESSING",
        "symbol": "BTC_BRL",
        "timestamp": 1571122877244,
        "type": "LIMIT",
        "value": "35"
    },
    "message": "Success"
}

```


### Caminho de requisição

`POST /v1/orders/create`

### Parâmetro de requisição



* Campo: symbol
  * Mandatório: true
  * Tipo: string
  * Detalhes: Símbolo da negociação, como BTC_BRL
* Campo: type
  * Mandatório: true
  * Tipo: string
  * Detalhes: Tipo de ordem, informe: LIMIT
* Campo: side
  * Mandatório: true
  * Tipo: string
  * Detalhes: A ponta executora da ordem, informe: SELL
* Campo: amount
  * Mandatório: true
  * Tipo: string
  * Detalhes: Quantidade da moeda de base, por exemplo, a quantidade de BTC na venda de BTC_BRL
* Campo: price
  * Mandatório: true
  * Tipo: string
  * Detalhes: Preço de venda
* Campo: accountId
  * Mandatório: false
  * Tipo: string
  * Detalhes: ID da subconta, se não for informado, a ordem será colocada sob conta principal


### Detalhes de resposta


|Campo       |Tipo  |Detalhes                                      |
|------------|------|----------------------------------------------|
|id          |string|ID da ordem                                   |
|symbol      |string|Símbolo da negociação                         |
|type        |string|Tipo da ordem                                 |
|side        |string|A ponta executora da ordem                    |
|price       |string|Preço unitário da ordem                       |
|averagePrice|string|Preço médio da ordem                          |
|amount      |string|Quantidade da moeda de base                   |
|filledAmount|string|Quantidade executada da moeda de base         |
|value       |string|Quantidade da moeda de cotação                |
|filledValue |string|Quantidade executada da moeda de cotação      |
|filledFee   |string|Taxa paga                                     |
|status      |string|Status da ordem(consultar introdução de ordem)|
|timestamp   |number|Hora da criação da ordem                      |


Criar ordem a mercado de compra
-------------------------------

Abre uma ordem a mercado de compra e a envia para livro de ofertas para sua execução.

```
curl -X POST \
  'https://api.novadax.com/v1/orders/create' \
  -H 'Content-Type: application/json' \
  -H 'X-Nova-Access-Key: your access key' \
  -H 'X-Nova-Signature: signature' \
  -H 'X-Nova-Timestamp: timestamp now' \
  -d '{
    "symbol": "BTC_BRL",
    "type": "MARKET",
    "side": "BUY",
    "value": "10.00"
  }'

```


```
from novadax import RequestClient as NovaClient

nova_client = NovaClient('your access key', 'your secret key')

result = nova_client.create_order('BTC_BRL', 'MARKET', 'BUY', value = '10.00')

print(result)

```


> Conteúdo de resposta

```
{
    "code": "A10000",
    "data": {
        "amount": null,
        "averagePrice": null,
        "filledAmount": "0",
        "filledFee": "0",
        "filledValue": "0",
        "id": "633681498843828224",
        "price": null,
        "side": "BUY",
        "status": "PROCESSING",
        "symbol": "BTC_BRL",
        "timestamp": 1571123042566,
        "type": "MARKET",
        "value": "10"
    },
    "message": "Success"
}

```


### Caminho de requisição

`POST /v1/orders/create`

### Parâmetro de requisição



* Campo: symbol
  * Mandatório: true
  * Tipo: string
  * Detalhes: Símbolo da negociação, como BTC_BRL
* Campo: type
  * Mandatório: true
  * Tipo: string
  * Detalhes: Tipo de ordem, informe: MARKET
* Campo: side
  * Mandatório: true
  * Tipo: string
  * Detalhes: A ponta executora da ordem, informe: BUY
* Campo: value
  * Mandatório: true
  * Tipo: string
  * Detalhes: Quantidade da moeda de cotação, por exemplo, a quantidade de BRL na compra de BTC_BRL
* Campo: accountId
  * Mandatório: false
  * Tipo: string
  * Detalhes: ID da subconta, se não for informado, a ordem será colocada sob conta principal


### Detalhes de resposta


|Campo       |Tipo  |Detalhes                                      |
|------------|------|----------------------------------------------|
|id          |string|ID da ordem                                   |
|symbol      |string|Símbolo da negociação                         |
|type        |string|Tipo da ordem                                 |
|side        |string|A ponta executora da ordem                    |
|price       |string|Preço unitário da ordem                       |
|averagePrice|string|Preço médio da ordem                          |
|amount      |string|Quantidade da moeda de base                   |
|filledAmount|string|Quantidade executada da moeda de base         |
|value       |string|Quantidade da moeda de cotação                |
|filledValue |string|Quantidade executada da moeda de cotação      |
|filledFee   |string|Taxa paga                                     |
|status      |string|Status da ordem(consultar introdução de ordem)|
|timestamp   |number|Hora da criação da ordem                      |


Criar ordem a mercado de venda
------------------------------

Abre uma ordem a mercado de venda e a envia para livro de ofertas para sua execução.

```
curl -X POST \
  'https://api.novadax.com/v1/orders/create' \
  -H 'Content-Type: application/json' \
  -H 'X-Nova-Access-Key: your access key' \
  -H 'X-Nova-Signature: signature' \
  -H 'X-Nova-Timestamp: timestamp now' \
  -d '{
    "symbol": "BTC_BRL",
    "type": "MARKET",
    "side": "SELL",
    "amount": "0.001"
  }'

```


```
from novadax import RequestClient as NovaClient

nova_client = NovaClient('your access key', 'your secret key')

result = nova_client.create_order('BTC_BRL', 'MARKET', 'SELL', amount = '0.001')

print(result)

```


> Conteúdo de resposta

```
{
    "code": "A10000",
    "data": {
        "amount": "0.001",
        "averagePrice": null,
        "filledAmount": "0",
        "filledFee": "0",
        "filledValue": "0",
        "id": "633682053058187264",
        "price": null,
        "side": "SELL",
        "status": "PROCESSING",
        "symbol": "BTC_BRL",
        "timestamp": 1571123174702,
        "type": "MARKET",
        "value": null
    },
    "message": "Success"
}

```


### Caminho de requisição

`POST /v1/orders/create`

### Parâmetro de requisição



* Campo: symbol
  * Mandatório: true
  * Tipo: string
  * Detalhes: Símbolo da negociação, como BTC_BRL
* Campo: type
  * Mandatório: true
  * Tipo: string
  * Detalhes: Tipo de ordem, informe: MARKET
* Campo: side
  * Mandatório: true
  * Tipo: string
  * Detalhes: A ponta executora da ordem, informe: SELL
* Campo: amount
  * Mandatório: true
  * Tipo: string
  * Detalhes: Quantidade da moeda de cotação, por exemplo, a quantidade de BRL na venda de BTC_BRL
* Campo: accountId
  * Mandatório: false
  * Tipo: string
  * Detalhes: ID da subconta, se não for informado, a ordem será colocada sob conta principal


### Detalhes de resposta


|Campo       |Tipo  |Detalhes                                      |
|------------|------|----------------------------------------------|
|id          |string|ID da ordem                                   |
|symbol      |string|Símbolo da negociação                         |
|type        |string|Tipo da ordem                                 |
|side        |string|A ponta executora da ordem                    |
|price       |string|Preço unitário da ordem                       |
|averagePrice|string|Preço médio da ordem                          |
|amount      |string|Quantidade da moeda de base                   |
|filledAmount|string|Quantidade executada da moeda de base         |
|value       |string|Quantidade da moeda de cotação                |
|filledValue |string|Quantidade executada da moeda de cotação      |
|filledFee   |string|Taxa paga                                     |
|status      |string|Status da ordem(consultar introdução de ordem)|
|timestamp   |number|Hora da criação da ordem                      |


Crair stop-limit ordem de compra
--------------------------------

Abre uma ordem limitada de compra e a envia para livro de ofertas para sua execução.

```
curl -X POST \
  'https://api.novadax.com/v1/orders/create' \
  -H 'Content-Type: application/json' \
  -H 'X-Nova-Access-Key: your access key' \
  -H 'X-Nova-Signature: signature' \
  -H 'X-Nova-Timestamp: timestamp now' \
  -d '{
    "symbol": "BTC_BRL",
    "type": "STOP_LIMIT",
    "side": "BUY",
    "price": "210000",
    "amount": "0.001",
    "operator: "GTE",
    "stopPrice: "211000"
'

```


```
from novadax import RequestClient as NovaClient

nova_client = NovaClient('your access key', 'your secret key')

result = nova_client.create_order('BTC_BRL', 'STOP_LIMIT', 'BUY', price='210000', amount='0.001', operator='GTE', stop_price="211000")

print(result)

```


> Conteúdo de resposta

```
{
    "code": "A10000",
    "data": {
        "amount": "0.001",
        "averagePrice": null,
        "filledAmount": "0",
        "filledFee": "0",
        "filledValue": "0",
        "id": "870613508008464384",
        "operator": "GTE",
        "price": "210000",
        "side": "BUY",
        "status": "SUBMITTED",
        "stopPrice": "211000",
        "symbol": "BTC_BRL",
        "timestamp": 1627612035528,
        "type": "STOP_LIMIT",
        "value": "210"
    },
    "message": "Success"
}

```


### Caminho de requisição

`POST /v1/orders/create`

### Parâmetro de requisição



* Campo: symbol
  * Mandatório: true
  * Tipo: string
  * Detalhes: Símbolo da negociação, como BTC_BRL
* Campo: type
  * Mandatório: true
  * Tipo: string
  * Detalhes: Tipo de ordem, informe: STOP_LIMIT
* Campo: side
  * Mandatório: true
  * Tipo: string
  * Detalhes: A ponta executora da ordem, informe: BUY
* Campo: amount
  * Mandatório: true
  * Tipo: string
  * Detalhes: Quantidade da moeda de base, por exemplo, a quantidade de BTC na compra de BTC_BRL
* Campo: price
  * Mandatório: true
  * Tipo: string
  * Detalhes: Preço de compra
* Campo: operator
  * Mandatório: true
  * Tipo: string
  * Detalhes: Operador de stop ordem(para mais informações, veja a introdução de ordem)
* Campo: stopPrice
  * Mandatório: true
  * Tipo: string
  * Detalhes: Preço de disparo
* Campo: accountId
  * Mandatório: false
  * Tipo: string
  * Detalhes: ID da subconta, se não for informado, a ordem será colocada sob conta principal


### Detalhes de resposta


|Campo       |Tipo  |Detalhes                                                                 |
|------------|------|-------------------------------------------------------------------------|
|id          |string|ID da ordem                                                              |
|symbol      |string|Símbolo da negociação                                                    |
|type        |string|Tipo da ordem                                                            |
|side        |string|A ponta executora da ordem                                               |
|price       |string|Preço unitário da ordem                                                  |
|averagePrice|string|Preço médio da ordem                                                     |
|amount      |string|Quantidade da moeda de base                                              |
|filledAmount|string|Quantidade executada da moeda de base                                    |
|value       |string|Quantidade da moeda de cotação                                           |
|filledValue |string|Quantidade executada da moeda de cotação                                 |
|filledFee   |string|Taxa paga                                                                |
|operator    |string|Operador de stop ordem(para mais informações, veja a introdução de ordem)|
|stopPrice   |string|Preço de disparo                                                         |
|status      |string|Status da ordem(consultar introdução de ordem)                           |
|timestamp   |number|Hora da criação da ordem                                                 |


Crair stop-limit ordem de venda
-------------------------------

Abre uma ordem limitada de venda e a envia para livro de ofertas para sua execução.

```
curl -X POST \
  'https://api.novadax.com/v1/orders/create' \
  -H 'Content-Type: application/json' \
  -H 'X-Nova-Access-Key: your access key' \
  -H 'X-Nova-Signature: signature' \
  -H 'X-Nova-Timestamp: timestamp now' \
  -d '{
    "symbol": "BTC_BRL",
    "type": "STOP_LIMIT",
    "side": "SELL",
    "price": "180000",
    "stopPrice":"190000",
    "operator":"LTE",
    "amount": "0.001"
  }'

```


```
from novadax import RequestClient as NovaClient

nova_client = NovaClient('your access key', 'your secret key')

result = nova_client.create_order('BTC_BRL', 'STOP_LIMIT', 'SELL', price='180000', amount='0.001', operator='LTE', stop_price="190000")

print(result)

```


> Conteúdo de resposta

```
{
    "code": "A10000",
    "data": {
        "amount": "0.001",
        "averagePrice": null,
        "filledAmount": "0",
        "filledFee": "0",
        "filledValue": "0",
        "id": "870616419635580928",
        "operator": "LTE",
        "price": "180000",
        "side": "SELL",
        "status": "SUBMITTED",
        "stopPrice": "190000",
        "symbol": "BTC_BRL",
        "timestamp": 1627612729713,
        "type": "STOP_LIMIT",
        "value": "180"
    },
    "message": "Success"
}

```


### Caminho de requisição

`POST /v1/orders/create`

### Parâmetro de requisição



* Campo: symbol
  * Mandatório: true
  * Tipo: string
  * Detalhes: Símbolo da negociação, como BTC_BRL
* Campo: type
  * Mandatório: true
  * Tipo: string
  * Detalhes: Tipo de ordem, informe: STOP_LIMIT
* Campo: side
  * Mandatório: true
  * Tipo: string
  * Detalhes: A ponta executora da ordem, informe: SELL
* Campo: amount
  * Mandatório: true
  * Tipo: string
  * Detalhes: Quantidade da moeda de base, por exemplo, a quantidade de BTC na venda de BTC_BRL
* Campo: price
  * Mandatório: true
  * Tipo: string
  * Detalhes: Preço de venda
* Campo: operator
  * Mandatório: true
  * Tipo: string
  * Detalhes: Operador de stop ordem(para mais informações, veja a introdução de ordem)
* Campo: stopPrice
  * Mandatório: true
  * Tipo: string
  * Detalhes: Preço de disparo
* Campo: accountId
  * Mandatório: false
  * Tipo: string
  * Detalhes: ID da subconta, se não for informado, a ordem será colocada sob conta principal


### Detalhes de resposta


|Campo       |Tipo  |Detalhes                                                                 |
|------------|------|-------------------------------------------------------------------------|
|id          |string|ID da ordem                                                              |
|symbol      |string|Símbolo da negociação                                                    |
|type        |string|Tipo da ordem                                                            |
|side        |string|A ponta executora da ordem                                               |
|price       |string|Preço unitário da ordem                                                  |
|averagePrice|string|Preço médio da ordem                                                     |
|amount      |string|Quantidade da moeda de base                                              |
|filledAmount|string|Quantidade executada da moeda de base                                    |
|value       |string|Quantidade da moeda de cotação                                           |
|filledValue |string|Quantidade executada da moeda de cotação                                 |
|filledFee   |string|Taxa paga                                                                |
|operator    |string|Operador de stop ordem(para mais informações, veja a introdução de ordem)|
|stopPrice   |string|Preço de disparo                                                         |
|status      |string|Status da ordem(consultar introdução de ordem)                           |
|timestamp   |number|Hora da criação da ordem                                                 |


Criar stop-market ordem de compra
---------------------------------

Abre uma ordem a mercado de compra e a envia para livro de ofertas para sua execução.

```
curl -X POST \
  'https://api.novadax.com/v1/orders/create' \
  -H 'Content-Type: application/json' \
  -H 'X-Nova-Access-Key: your access key' \
  -H 'X-Nova-Signature: signature' \
  -H 'X-Nova-Timestamp: timestamp now' \
  -d '{
    "symbol": "BTC_BRL",
    "type": "STOP_MARKET",
    "side": "BUY",
    "value": "10.00",
    "stopPrice": "211000",
    "operator": "GTE"
  }'

```


```
from novadax import RequestClient as NovaClient

nova_client = NovaClient('your access key', 'your secret key')

result = nova_client.create_order('BTC_BRL', 'STOP_MARKET', 'BUY', value='25', operator="GTE", stop_price="211000")

print(result)

```


> Conteúdo de resposta

```
{
    "code": "A10000",
    "data": {
        "amount": null,
        "averagePrice": null,
        "filledAmount": "0",
        "filledFee": "0",
        "filledValue": "0",
        "id": "870617243765985280",
        "operator": "GTE",
        "price": "205118.95",
        "side": "BUY",
        "status": "SUBMITTED",
        "stopPrice": "211000",
        "symbol": "BTC_BRL",
        "timestamp": 1627612926202,
        "type": "STOP_MARKET",
        "value": "25"
    },
    "message": "Success"
}

```


### Caminho de requisição

`POST /v1/orders/create`

### Parâmetro de requisição



* Campo: symbol
  * Mandatório: true
  * Tipo: string
  * Detalhes: Símbolo da negociação, como BTC_BRL
* Campo: type
  * Mandatório: true
  * Tipo: string
  * Detalhes: Tipo de ordem, informe: STOP_MARKET
* Campo: side
  * Mandatório: true
  * Tipo: string
  * Detalhes: A ponta executora da ordem, informe: BUY
* Campo: value
  * Mandatório: true
  * Tipo: string
  * Detalhes: Quantidade da moeda de cotação, por exemplo, a quantidade de BRL na compra de BTC_BRL
* Campo: operator
  * Mandatório: true
  * Tipo: string
  * Detalhes: Operador de stop ordem(para mais informações, veja a introdução de ordem)
* Campo: stopPrice
  * Mandatório: true
  * Tipo: string
  * Detalhes: Preço de disparo
* Campo: accountId
  * Mandatório: false
  * Tipo: string
  * Detalhes: ID da subconta, se não for informado, a ordem será colocada sob conta principal


### Detalhes de resposta


|Campo       |Tipo  |Detalhes                                                                 |
|------------|------|-------------------------------------------------------------------------|
|id          |string|ID da ordem                                                              |
|symbol      |string|Símbolo da negociação                                                    |
|type        |string|Tipo da ordem                                                            |
|side        |string|A ponta executora da ordem                                               |
|price       |string|Preço unitário da ordem                                                  |
|averagePrice|string|Preço médio da ordem                                                     |
|amount      |string|Quantidade da moeda de base                                              |
|filledAmount|string|Quantidade executada da moeda de base                                    |
|value       |string|Quantidade da moeda de cotação                                           |
|filledValue |string|Quantidade executada da moeda de cotação                                 |
|filledFee   |string|Taxa paga                                                                |
|operator    |string|Operador de stop ordem(para mais informações, veja a introdução de ordem)|
|stopPrice   |string|Preço de disparo                                                         |
|status      |string|Status da ordem(consultar introdução de ordem)                           |
|timestamp   |number|Hora da criação da ordem                                                 |


Criar stop-market ordem de venda
--------------------------------

Abre uma ordem a mercado de venda e a envia para livro de ofertas para sua execução.

```
curl -X POST \
  'https://api.novadax.com/v1/orders/create' \
  -H 'Content-Type: application/json' \
  -H 'X-Nova-Access-Key: your access key' \
  -H 'X-Nova-Signature: signature' \
  -H 'X-Nova-Timestamp: timestamp now' \
  -d '{
    "symbol": "BTC_BRL",
    "type": "STOP_MARKET",
    "side": "SELL",
    "amount": "0.001",
    "stopPrice": "180000",
    "operator": "LTE"
  }'

```


```
from novadax import RequestClient as NovaClient

nova_client = NovaClient('your access key', 'your secret key')

result = nova_client.create_order('BTC_BRL', 'STOP_MARKET', 'SELL', amount='0.001', operator="LTE", stop_price="180000")

print(result)

```


> Conteúdo de resposta

```
{
    "code": "A10000",
    "data": {
        "amount": "0.001",
        "averagePrice": null,
        "filledAmount": "0",
        "filledFee": "0",
        "filledValue": "0",
        "id": "870617706309636096",
        "operator": "LTE",
        "price": "204712.39",
        "side": "SELL",
        "status": "SUBMITTED",
        "stopPrice": "180000",
        "symbol": "BTC_BRL",
        "timestamp": 1627613036480,
        "type": "STOP_MARKET",
        "value": null
    },
    "message": "Success"
}

```


### Caminho de requisição

`POST /v1/orders/create`

### Parâmetro de requisição



* Campo: symbol
  * Mandatório: true
  * Tipo: string
  * Detalhes: Símbolo da negociação, como BTC_BRL
* Campo: type
  * Mandatório: true
  * Tipo: string
  * Detalhes: Tipo de ordem, informe: STOP_MARKET
* Campo: side
  * Mandatório: true
  * Tipo: string
  * Detalhes: A ponta executora da ordem, informe: SELL
* Campo: amount
  * Mandatório: true
  * Tipo: string
  * Detalhes: Quantidade da moeda de cotação, por exemplo, a quantidade de BRL na venda de BTC_BRL
* Campo: operator
  * Mandatório: true
  * Tipo: string
  * Detalhes: Operador de stop ordem(para mais informações, veja a introdução de ordem)
* Campo: stopPrice
  * Mandatório: true
  * Tipo: string
  * Detalhes: Preço de disparo
* Campo: accountId
  * Mandatório: false
  * Tipo: string
  * Detalhes: ID da subconta, se não for informado, a ordem será colocada sob conta principal


### Detalhes de resposta


|Campo       |Tipo  |Detalhes                                                                 |
|------------|------|-------------------------------------------------------------------------|
|id          |string|ID da ordem                                                              |
|symbol      |string|Símbolo da negociação                                                    |
|type        |string|Tipo da ordem                                                            |
|side        |string|A ponta executora da ordem                                               |
|price       |string|Preço unitário da ordem                                                  |
|averagePrice|string|Preço médio da ordem                                                     |
|amount      |string|Quantidade da moeda de base                                              |
|filledAmount|string|Quantidade executada da moeda de base                                    |
|value       |string|Quantidade da moeda de cotação                                           |
|filledValue |string|Quantidade executada da moeda de cotação                                 |
|filledFee   |string|Taxa paga                                                                |
|operator    |string|Operador de stop ordem(para mais informações, veja a introdução de ordem)|
|stopPrice   |string|Preço de disparo                                                         |
|status      |string|Status da ordem(consultar introdução de ordem)                           |
|timestamp   |number|Hora da criação da ordem                                                 |


Cancelar ordem
--------------

Solicita o cancelamento de uma ordem.

```
curl -X POST \
  'https://api.novadax.com/v1/orders/cancel' \
  -H 'Content-Type: application/json' \
  -H 'X-Nova-Access-Key: your access key' \
  -H 'X-Nova-Signature: signature' \
  -H 'X-Nova-Timestamp: timestamp now' \
  -d '{
    "id": "608705793440616448"
  }'

```


```
from novadax import RequestClient as NovaClient

nova_client = NovaClient('your access key', 'your secret key')

result = nova_client.cancle_order("608705793440616448")

print(result)

```


> Conteúdo de resposta

```
{
    "code": "A10000",
    "data": {
        "result": true
    },
    "message": "Success"
}

```


### Caminho de requisição

`POST /v1/orders/cancel`

### Parâmetro de requisição


|Campo|Mandatório|Tipo  |Detalhes   |
|-----|----------|------|-----------|
|id   |true      |string|ID da ordem|


### Detalhes de resposta


|Campo |Tipo  |Detalhes                 |
|------|------|-------------------------|
|result|string|Resultado do cancelamento|


Conferir detalhes de ordens
---------------------------

Retorna os detalhes atualizados de uma ordem. Ordens criadas via API não serão encontradas depois de duas horas após seu cancelamento.

```
curl -X GET \
  'https://api.novadax.com/v1/orders/get?id=608695623247466496' \
  -H 'X-Nova-Access-Key: your access key' \
  -H 'X-Nova-Signature: signature' \
  -H 'X-Nova-Timestamp: timestamp now'

```


```
from novadax import RequestClient as NovaClient

nova_client = NovaClient('your access key', 'your secret key')

result = nova_client.get_order('608695623247466496')

print(result)

```


> Conteúdo de resposta

```
{
    "code": "A10000",
    "data": {
        "id": "608695623247466496",
        "symbol": "BTC_BRL",
        "type": "MARKET",
        "side": "SELL",
        "price": null,
        "averagePrice": "0",
        "amount": "0.123",
        "filledAmount": "0",
        "value": null,
        "filledValue": "0",
        "filledFee": "0",
        "stopPrice": null,
        "operator": null,
        "status": "REJECTED",
        "timestamp": 1565165945588
    },
    "message": "Success"
}

```


### Caminho de requisição

`GET /v1/orders/get`

### Parâmetro de requisição


|Campo|Mandatório|Tipo  |Detalhes   |
|-----|----------|------|-----------|
|id   |True      |string|ID da ordem|


### Detalhes de resposta


|Campo       |Tipo  |Detalhes                                       |
|------------|------|-----------------------------------------------|
|id          |string|ID da ordem                                    |
|symbol      |string|Símbolo da negociação                          |
|type        |string|Tipo de ordem(consultar introdução de ordem)   |
|side        |string|A ponta executora da ordem, opções: BUY or SELL|
|price       |string|Preço unitário de ordem                        |
|averagePrice|string|Preço médio da ordem                           |
|amount      |string|Quantidade da moeda de base                    |
|filledAmount|string|Quantidade executada da moeda de base          |
|value       |string|Quantidade da moeda de cotação                 |
|filledValue |string|Quantidade executada da moeda de cotação       |
|filledFee   |string|Taxa paga                                      |
|status      |string|Status da ordem(consultar introdução de ordem) |
|timestamp   |number|Hora da criação da ordem                       |


Conferir histórico de ordens
----------------------------

Retorna o histório de ordens de acordo com o filtro informado. Ordens criadas via API não serão encontradas depois de duas horas após seu cancelamento.

```
curl -X GET \
  'https://api.novadax.com/v1/orders/list?symbol=BTC_BRL&status=FINISHED&from=608705793440616448&to=608694995137859584&limit=100' \
  -H 'X-Nova-Access-Key: your access key' \
  -H 'X-Nova-Signature: signature' \
  -H 'X-Nova-Timestamp: timestamp now'

```


```
from novadax import RequestClient as NovaClient

nova_client = NovaClient('your access key', 'your secret key')

result = nova_client.list_orders('BTC_BRL', 'FINISHED', '608705793440616448', '608694995137859584', '100')

print(result)

```


> Conteúdo de resposta

```
{
    "code": "A10000",
    "data": [
        {
            "id": "608695678650028032",
            "symbol": "BTC_BRL",
            "type": "MARKET",
            "side": "SELL",
            "price": null,
            "averagePrice": "0",
            "amount": "0.123",
            "filledAmount": "0",
            "value": null,
            "filledValue": "0",
            "filledFee": "0",
            "stopPrice": null,
            "operator": null,
            "status": "REJECTED",
            "timestamp": 1565165958796
        },
        {
            "id": "608695623247466496",
            "symbol": "BTC_BRL",
            "type": "MARKET",
            "side": "SELL",
            "price": null,
            "averagePrice": "0",
            "amount": "0.123",
            "filledAmount": "0",
            "value": null,
            "filledValue": "0",
            "filledFee": "0",
            "stopPrice": null,
            "operator": null,
            "status": "REJECTED",
            "timestamp": 1565165945588
        }
    ],
    "message": "Success"
}

```


### Caminho de requisição

`GET /v1/orders/list`

### Parâmetro de requisição



* Campo: symbol
  * Mandatório: false
  * Tipo: string
  * Detalhes: Símbolo da negociação, como BTC_BRL
* Campo: status
  * Mandatório: false
  * Tipo: string
  * Detalhes: Você pode encontrar as opções dos status de ordem na introdução. Use vírgula para separar vários status. Você pode pesquisar “FINISHED” para encontrar ordens não concluídas, e pesquisar “UNFINISHED” para encontrar ordens concluídas.
* Campo: fromId
  * Mandatório: false
  * Tipo: string
  * Detalhes: Ordem de início
* Campo: toId
  * Mandatório: false
  * Tipo: string
  * Detalhes: Ordem de término
* Campo: fromTimestamp
  * Mandatório: false
  * Tipo: number
  * Detalhes: Data de início, considerando a hora da criação da ordem em milissegundos
* Campo: toTimestamp
  * Mandatório: false
  * Tipo: number
  * Detalhes: Data de término, considerando a hora da criação da ordem em milissegundos
* Campo: limit
  * Mandatório: false
  * Tipo: string
  * Detalhes: O número de ordens a retornar, default 100, máx 100
* Campo: accountId
  * Mandatório: false
  * Tipo: string
  * Detalhes: ID da subconta, se não for informado, a ordem será colocada sob conta principal


### Detalhes de resposta


|Campo       |Tipo  |Detalhes                                      |
|------------|------|----------------------------------------------|
|id          |string|ID da ordem                                   |
|symbol      |string|Símbolo da negociação                         |
|type        |string|Tipo de ordem                                 |
|side        |string|A ponta executora da ordem                    |
|price       |string|Preço unitário de ordem                       |
|averagePrice|string|Preço médio da ordem                          |
|amount      |string|Quantidade da moeda de base                   |
|filledAmount|string|Quantidade executada da moeda de base         |
|value       |string|Quantidade da moeda de cotação                |
|filledValue |string|Quantidade executada da moeda de cotação      |
|filledFee   |string|Taxa paga                                     |
|status      |string|Status da ordem(consultar introdução de ordem)|
|timestamp   |number|Hora da criação da ordem                      |


Conferir detalhes de execução de ordens
---------------------------------------

Retorna o resultado da execução de uma ordem.

```
curl -X GET \
  'https://api.novadax.com/v1/orders/fills?orderId=608716957545402368' \
  -H 'X-Nova-Access-Key: your access key' \
  -H 'X-Nova-Signature: signature' \
  -H 'X-Nova-Timestamp: timestamp now'

```


```
from novadax import RequestClient as NovaClient

nova_client = NovaClient('your access key', 'your secret key')

result = nova_client.list_order_fills('608716957545402368')

print(result)

```


> Conteúdo de resposta

```
{
    "code": "A10000",
    "data": [
        {
            "id": "608717046691139584",
            "orderId": "608716957545402368",
            "symbol": "BTC_BRL",
            "side": "BUY",
            "amount": "0.0988",
            "price": "45514.76",
            "fee": "0.0000988 BTC",
            "feeAmount": "0.0000988",
            "feeCurrency": "BTC",
            "role": "MAKER",
            "timestamp": 1565171053345
        },
        {
            "id": "608717065729085441",
            "orderId": "608716957545402368",
            "symbol": "BTC_BRL",
            "side": "BUY",
            "amount": "0.0242",
            "price": "45514.76",
            "fee": "0.0000242 BTC",
            "feeAmount": "0.0000988",
            "feeCurrency": "BTC",
            "role": "MAKER",
            "timestamp": 1565171057882
        }
    ],
    "message": "Success"
}

```


### Caminho de requisição

`GET /v1/orders/fills`

### Parâmetro de requisição



* Campo: orderId
  * Mandatório: true
  * Tipo: string
  * Detalhes: ID da ordem
* Campo: symbol
  * Mandatório: false
  * Tipo: string
  * Detalhes: Símbolo da negociação, como BTC_BRL
* Campo: fromId
  * Mandatório: false
  * Tipo: string
  * Detalhes: Ordem de início
* Campo: toId
  * Mandatório: false
  * Tipo: string
  * Detalhes: Ordem de término
* Campo: fromTimestamp
  * Mandatório: false
  * Tipo: number
  * Detalhes: Data de início, considerando a hora da execução da ordem em milissegundos
* Campo: toTimestamp
  * Mandatório: false
  * Tipo: number
  * Detalhes: Data de término, considerando a hora da execução da ordem em milissegundos
* Campo: limit
  * Mandatório: false
  * Tipo: string
  * Detalhes: O número de ordens a retornar, default 100, máx 100
* Campo: accountId
  * Mandatório: false
  * Tipo: string
  * Detalhes: ID da subconta, se não for informado, a ordem será colocada sob conta principal


### Detalhes de resposta


|Campo        |Tipo  |Detalhes                                                 |
|-------------|------|---------------------------------------------------------|
|id           |string|ID da execução de ordem                                  |
|orderId      |string|ID da ordem                                              |
|symbol       |string|Símbolo da negociação                                    |
|side         |string|A ponta executora da ordem(consultar introdução de ordem)|
|price        |string|Preço executado                                          |
|amount       |string|Quantidade executada                                     |
|fee          |string|Taxa paga                                                |
|feeCurrency  |string|Moeda de taxa                                            |
|feeAmount    |string|Quantidade de moeda de taxa                              |
|role         |string|Papel na execução(consultar introdução de ordem)         |
|fromTimestamp|false |number                                                   |


Obter saldo da conta
--------------------

Retorna os saldos das moedas da sua conta.

```
curl 'https://api.novadax.com/v1/account/getBalance'

```


```
from novadax import RequestClient as NovaClient

nova_client = NovaClient('your access key', 'your secret key')

result = nova_client.get_account_balance()

print(result)

```


> Conteúdo de resposta

```
{
    "code": "A10000",
    "data": [
        {
            "available": "1.23",
            "balance": "0.23",
            "currency": "BTC",
            "hold": "1"
        }
    ],
    "message": "Success"
}

```


### Caminho de requisição

`GET /v1/account/getBalance`

### Parâmetro de requisição

Nenhum parâmetro é necessário para este endpoint.

### Detalhes de resposta


|Campo    |Tipo  |Detalhes          |
|---------|------|------------------|
|currency |string|A moeda do saldo  |
|balance  |string|O saldo da moeda  |
|hold     |string|O saldo em ordens |
|available|string|O saldo disponível|
|accountId|string|O ID da conta     |


Sacar criptomoedas
------------------

```
curl -X POST \
  'https://api.novadax.com/v1/account/withdraw/coin' \
  -H 'Content-Type: application/json' \
  -H 'X-Nova-Access-Key: your access key' \
  -H 'X-Nova-Signature: signature' \
  -H 'X-Nova-Timestamp: timestamp now' \
  -d '{
    "code": "BTC",
    "amount": "1",
    "wallet": "your to_address"
  }'

```


```
from novadax import RequestClient as NovaClient

nova_client = NovaClient('your access key', 'your secret key')

result = nova_client.withdraw_coin("BTC","1",'...')

print(result)

```


> Conteúdo de resposta

```
{
  "code":"A10000",
  "data": "DR123",
  "message":"Success"
}

```


### Caminho de requisição

`POST /v1/account/withdraw/coin`

### Parâmetro de requisição


|Campo     |Mandatório|Tipo  |Detalhes                                         |
|----------|----------|------|-------------------------------------------------|
|code      |ture      |string|Símbolo da moeda, como BTC                       |
|amount    |true      |string|Valor do saque                                   |
|wallet    |true      |string|Endereço de carteira de destino                  |
|chainAlias|true      |string|Endereço de carteira de destino,apelido de cadeia|
|tag       |false     |string|Tag, requerido ao enviar XLM, XRP e EOS          |


### Detalhes de resposta

O campo data retorna o id do saque.

Lista de sub-conta
------------------

> Retorna a lista de todas as subcontas da conta principal

```
curl "https://api.novadax.com/v1/account/subs"

```


```
from novadax import RequestClient as NovaClient

nova_client = NovaClient('your access key', 'your secret key')
subs = self.api.subs()

```


> Conteúdo de resposta

```
{
    "code": "A10000",
    "data": [{
        "subId": "CA648856083527372800",
        "state": "Normal",
        "subAccount": "003",
        "subIdentify": "003"
    }],
    "message": "Success"
}

```


### Caminho de requisição

`GET /v1/account/subs`

### Parâmetro de requisição

Nenhum parâmetro é necessário para este endpoint.

### Detalhes de resposta


|Campo      |Tipo  |Detalhes                                               |
|-----------|------|-------------------------------------------------------|
|subId      |string|ID da subconta                                         |
|state      |string|Status da subconta, Normal/Frozen (normal ou bloqueada)|
|subAccount |string|Nome da subconta                                       |
|subIdentify|string|Descrição da subconta                                  |


Saldo da sub-conta
------------------

> Retorna o saldo da subconta

```
curl "https://api.novadax.com/v1/account/subs/balance?subId=xxx"

```


```
from novadax import RequestClient as NovaClient

nova_client = NovaClient('your access key', 'your secret key')
ava = self.api.subs_balance(subId)

```


> Conteúdo de resposta

```
{
    "code":"A10000",
    "data":[
        {
            "balance":"7.22",
            "currency":"BTC"
        }
    ],
    "message":"Success"
}

```


### Caminho de requisição

`GET /v1/account/subs/balance?subId=xxx`

### Parâmetro de requisição


|Campo|Mandatório|Tipo  |Detalhes      |
|-----|----------|------|--------------|
|subId|ture      |string|ID da subconta|


### Detalhes de resposta


|Campo   |Tipo  |Detalhes         |
|--------|------|-----------------|
|balance |string|Saldo da subconta|
|currency|string|Moeda do saldo   |


Histórico de transferência da sub-conta
---------------------------------------

> Retorna o histórico de transferências entre conta principal e subcontas

```
curl "https://api.novadax.com/v1/account/subs/transfer/record?subId=xxx"

```


```
from novadax import RequestClient as NovaClient

nova_client = NovaClient('your access key', 'your secret key')
ava = self.api.subs_transfer_record(subId)

```


> Conteúdo de resposta

```
{
    "code": "A10000",
    "data": [{
        "subId": "CA648855702269333504",
        "amount": "103.22",
        "currency": "BRL",
        "state": "success",
        "type": "master-transfer-out"
    }, {
        "subId": "CA648855702269333504",
        "amount": "3.5",
        "currency": "BRL",
        "state": "success",
        "type": "master-transfer-in"
    }],
    "message": "Success"
}

```


### Caminho de requisição

`GET /v1/account/subs/transfer/record?subId=xxx`

### Parâmetro de requisição


|Campo|Mandatório|Tipo  |Detalhes      |
|-----|----------|------|--------------|
|subId|ture      |string|ID da subconta|


### Detalhes de resposta



* Campo: subId
  * Tipo: string
  * Detalhes: ID da subconta
* Campo: amount
  * Tipo: string
  * Detalhes: Quantidade transferida
* Campo: currency
  * Tipo: string
  * Detalhes: Moeda da transferência
* Campo: state
  * Tipo: string
  * Detalhes: Status da transferência: success/fail (sucesso/falha)
* Campo: type
  * Tipo: string
  * Detalhes: Tipo: master-transfer-in (transferido para conta principal) ou master-transfer-out (transferido da conta principal)


Transferência da sub-conta
--------------------------

> Você pode utilizar este endpoint para transferir ativos entre conta principal e subcontas

```

from novadax import RequestClient as NovaClient

nova_client = NovaClient('your access key', 'your secret key')
subId = 'CA648856083527372800'
assetCode = 'BTC'
transferAmount = '0.51'
transferType = 'master-transfer-out'
tid = self.api.subs_transfer(subId, assetCode, transferAmount, transferType);
print(tid)

```


> Conteúdo de resposta

```
{
  "code":"A10000",
  "message":"Success",
  "data":40
}

```


### Caminho de requisição

`POST /v1/account/subs/transfer`

### Parâmetro de requisição


|Campo         |Mandatório|Tipo  |Detalhes                                      |
|--------------|----------|------|----------------------------------------------|
|subId         |true      |string|ID da subconta                                |
|currency      |true      |string|Moeda da transferência                        |
|transferAmount|true      |string|Quantidade da transferência                   |
|transferType  |true      |string|Tipo，master-transfer-in ou master-transfer-out|
|              |          |      |                                              |


### Detalhes de resposta

O campo "data" retorna o ID da transferência realizada.

Registros de depósito e saque de carteira
-----------------------------------------

The endpoint returns the records of deposits and withdraws.

```
curl -X GET \
   'https://api.novadax.com/v1/wallet/query/deposit-withdraw?currency=BTC&type=coin_in&start=DR562339304588709888&size=10&direct=desc'\
     -H 'X-Nova-Access-Key: your access key' \
     -H 'X-Nova-Signature: signature' \
     -H 'X-Nova-Timestamp: timestamp now' \

```


```
from novadax import RequestClient as NovaClient
nova_client = NovaClient('your access key', 'your secret key')
result = nova_client.wallet_deposit_withdraw_record()
print(result)

```


> Conteúdo de resposta

```
{
    "code": "A10000",
    "data": [{
        "id": "DR562339304588709888",
        "type": "COIN_IN",
        "currency": "XLM",
        "chain": "XLM",
        "address": "GCUTK7KHPJC3ZQJ3OMWWFHAK2OXIBRD4LNZQRCCOVE7A2XOPP2K5PU5Q",
        "addressTag": "1000009",
        "amount": 1.0,
        "state": "SUCCESS",
        "txHash": "39210645748822f8d4ce673c7559aa6622e6e9cdd7073bc0fcae14b1edfda5f4",
        "createdAt": 1554113737000,
        "updatedAt": 1601371273000
    }...],
    "message": "Success"
}

```


### Caminho de requisição

`GET /v1/wallet/query/deposit-withdraw`

### Parâmetro de requisição


|Campo   |Tipo  |Mandatório|Detalhes                             |Valor padrão        |
|--------|------|----------|-------------------------------------|--------------------|
|currency|string|false     |The currency code. e.g. BTC          |NA                  |
|type    |string|false     |default record type to search        |coin_in and coin_out|
|direct  |string|false     |the order of records,e.g. asc or desc|asc                 |
|size    |int   |false     |the number of iterms to return       |100                 |
|start   |string|false     |the id of record,                    |NA                  |


### Detalhes de resposta



* Campo: id
  * Tipo: string
  * Detalhes: the id of record
* Campo: type
  * Tipo: string
  * Detalhes: the type of record
* Campo: currency
  * Tipo: string
  * Detalhes: the currency code of record
* Campo: txHash
  * Tipo: string
  * Detalhes: the txid of chain
* Campo: address
  * Tipo: string
  * Detalhes: the dst address of txHash
* Campo: addressTag
  * Tipo: string
  * Detalhes: the tag of txHash
* Campo: chain
  * Tipo: string
  * Detalhes: Block chain name,internal means transfer through novadax inside rather than chain
* Campo: amount
  * Tipo: decimal
  * Detalhes: the amount of txHash
* Campo: state
  * Tipo: string
  * Detalhes: the state of record
* Campo: createdAt
  * Tipo: long
  * Detalhes: The timestamp in milliseconds for the transfer creation
* Campo: updatedAt
  * Tipo: long
  * Detalhes: The timestamp in milliseconds for the transfer's latest update


#### List of possible record state


|State         |Detalhes                                                    |
|--------------|------------------------------------------------------------|
|Pending       |the record is wait broadcast to chain                       |
|x/M confirming|the comfirming state of tx,the M is total confirmings needed|
|SUCCESS       |the record is success full                                  |
|FAIL          |the record failed                                           |


Sacar criptomoedas
------------------

```
curl -X POST \
  'https://api.novadax.com/v1/account/withdraw/coin' \
  -H 'Content-Type: application/json' \
  -H 'X-Nova-Access-Key: your access key' \
  -H 'X-Nova-Signature: signature' \
  -H 'X-Nova-Timestamp: timestamp now' \
  -d '{
    "code": "BTC",
    "amount": "1",
    "wallet": "your to_address"
  }'

```


```
from novadax import RequestClient as NovaClient

nova_client = NovaClient('your access key', 'your secret key')

result = nova_client.withdraw_coin("BTC","1",'...')

print(result)

```


> Conteúdo de resposta

```
{
  "code":"A10000",
  "data": "DR123",
  "message":"Success"
}

```


### Caminho de requisição

`POST /v1/account/withdraw/coin`

### Parâmetro de requisição


|Campo     |Mandatório|Tipo  |Detalhes                                         |
|----------|----------|------|-------------------------------------------------|
|code      |ture      |string|Símbolo da moeda, como BTC                       |
|amount    |true      |string|Valor do saque                                   |
|wallet    |true      |string|Endereço de carteira de destino                  |
|chainAlias|true      |string|Endereço de carteira de destino apelido de cadeia|
|tag       |false     |string|Tag, requerido ao enviar XLM, XRP e EOS          |


### Detalhes de resposta

O campo data retorna o id do saque.

Pesquisa em rede Chain
----------------------

```
curl -X GET \
  'https://api.novadax.com/v1/wallet/crypto/chain/{code}' \
  -H 'Content-Type: application/json' \
  -H 'X-Nova-Access-Key: your access key' \
  -H 'X-Nova-Signature: signature' \
  -H 'X-Nova-Timestamp: timestamp now' \

```


```
from novadax import RequestClient as NovaClient

nova_client = NovaClient('your access key', 'your secret key')

result = nova_client.crypto_chain("ALL")

print(result)

```


### Caminho de requisição

`GET /crypto/chain/{code}`

### Parâmetro de requisição


|param name|param type|param note                            |
|----------|----------|--------------------------------------|
|code      |String    |Código da conta,default value is "ALL"|


### Detalhes de resposta


|param name      |param type|param note                               |
|----------------|----------|-----------------------------------------|
|accountCode     |String    |Código da conta                          |
|accountType     |String    |Tipo da conta: DIGITAL, LEGAL            |
|accountPrecision|Integer   |Precisão da conta                        |
|accountOrder    |Integer   |Ordem da conta                           |
|accountState    |Integer   |Estado da conta. 1: em uso; 2; não em uso|
|tokens          |List      |Lista da rede de cripto                  |


> List of tokens


|parame name      |param type|param note                                                         |
|-----------------|----------|-------------------------------------------------------------------|
|codeAccount      |String    |Código da conta                                                    |
|chainAlias       |String    |Apelido da cadeia de cripto, usado para enviar cripto              |
|chainName        |String    |Nome da cadeia de cripto, usado só para ler                        |
|mainAddr         |String    |Endereço principal par criptos com memo. O valor default é nulo    |
|useMemo          |Integer   |Usar memo ou não. 1: usado; 0: não usado                           |
|useDynamicSendFee|String    |Usar taxas dinâmicas para envio. 1: usado; 0: não usado            |
|minConf          |Integer   |Confirmações mínimas da cadeia                                     |
|useFirst         |Integer   |A cadeia default será escolhida quando "chainAlias" for nulo em API|
|state            |Integer   |A cadeia está em uso. 1: em uso; 0: não em uso                     |
|chainURL         |String    |URL da cadeia                                                      |
|chainAddressURL  |String    |URL do endereço da cadeia                                          |
|chainHashURL     |String    |URL do hash da cadeia                                              |
|officialURL      |String    |URL da cadeia oficial                                              |


Acesso
------

NovaDAX WebSocket API baseia-se em Socket.io. Você pode encontrar mais informações sobre Socket.io em seu site oficial.

### Endereço

`wss://api.novadax.com`

```
// Socket.io exemplo de estabelecer conexão
const io = require("socket.io-client");

const socket = io("wss://api.novadax.com", {
transports: ['websocket']
});

// Socket.io exemplo de subscrição
socket.emit("SUBSCRIBE", ["MARKET.BTC_USDT.TICKER", "MARKET.BTC_USDT.TRADE"])
socket.on("MARKET.BTC_USDT.TICKER", (ticker) => {
    console.log(ticker)
})
socket.on("MARKET.BTC_USDT.TRADE", (trade) => {
    console.log(trade)
})

// Socket.io exemplo de cancelar subscrição
socket.emit("UNSUBSCRIBE", ["MARKET.BTC_USDT.DEPTH.LEVEL0"])

```


### Limite

*   Suporta apenas `websocket` transports de Socket.io
*   Um IP só pode estabelecer 10 conexões WebSocket

### Subscrever tópicos

Ao subscrever um tópico, você receberá todas as notificações relacionadas com ele. Formato de subscrição:

`socket.emit("SUBSCRIBE", ["XXX"])`

### Cancelar subscrição

Depois de subscrever um tópico, se você não quiser receber notificações sobre ele, pode cancelar a subscrição. Formato de cancelar a subscrição:

`socket.emit("UNSUBSCRIBE", ["XXX"])`

Subscrever dados de ticker de um par de negociação específico
-------------------------------------------------------------

Após a subscrição, o sistema enviará dados de ticker de todos os pares de negociação uma vez por segundo.

```
from novadax import WebSocketClient

nova_client = WebSocketClient('your access key(optional)', 'your secret key(optional)')

def tickers_handler(tickers):
    print('received tickers: ', tickers)

nova_client.sub_market_tickers(tickers_handler)

```


> Valor de retorno

```
[
  {
      "ask": "34708.15",
      "baseVolume24h": "34.08241488",
      "bid": "34621.74",
      "high24h": "35079.77",
      "lastPrice": "34669.81",
      "low24h": "34330.64",
      "open24h": "34492.08",
      "quoteVolume24h": "1182480.09502814",
      "symbol": "BTC_BRL",
      "timestamp": 1571112216346
  }
]

```


### Tópico de subscrição

`MARKET.TICKERS`

### Parâmetros de requisição

Nenhum parâmetro é necessário para este endpoint.

### Detalhes de resposta


|Campo         |Tipo  |Detalhes                                                     |
|--------------|------|-------------------------------------------------------------|
|symbol        |string|Símbolo da negociação                                        |
|lastPrice     |string|Preço unitário da última negociação                          |
|bid           |string|Maior preço de oferta de compra das últimas 24 horas         |
|ask           |string|Menor preço de oferta de venda das últimas 24 horas          |
|open24h       |string|Preço unitário de abertura de negociação das últimas 24 horas|
|high24h       |string|Maior preço unitário de negociação das últimas 24 horas      |
|low24h        |string|Menor preço unitário de negociação das últimas 24 horas      |
|baseVolume24h |string|Volume de negociação na moeda de base das últimas 24 horas   |
|quoteVolume24h|string|Volume de negociação na moeda de cotação das últimas 24 horas|
|timestamp     |number|A hora atual do sistema ajustada para o horário UTC          |


Subscrever dados de ticker de um único par de negociação
--------------------------------------------------------

Após a subscrição, o sistema enviará dados de ticker do par de negociação definido uma vez por segundo.

```
from novadax import WebSocketClient

nova_client = WebSocketClient('your access key(optional)', 'your secret key(optional)')

def ticker_handler(ticker):
    print('received ticker: ', ticker)

nova_client.sub_market_ticker("BTC_BRL", ticker_handler)

```


> Valor de retorno

```
{
    "ask": "34708.15",
    "baseVolume24h": "34.08241488",
    "bid": "34621.74",
    "high24h": "35079.77",
    "lastPrice": "34669.81",
    "low24h": "34330.64",
    "open24h": "34492.08",
    "quoteVolume24h": "1182480.09502814",
    "symbol": "BTC_BRL",
    "timestamp": 1571112216346
}

```


### Tópico de subscrição

`MARKET.{{symbol}}.TICKER`

### Parâmetros de requisição


|Campo |Mandatório|Tipo  |Detalhes             |
|------|----------|------|---------------------|
|symbol|true      |string|Símbolo da negociação|


### Detalhes de resposta


|Campo         |Tipo  |Detalhes                                                     |
|--------------|------|-------------------------------------------------------------|
|symbol        |string|Símbolo de negociação                                        |
|lastPrice     |string|Preço unitário da última negociação                          |
|bid           |string|Maior preço de oferta de compra das últimas 24 horas         |
|ask           |string|Menor preço de oferta de venda das últimas 24 horas          |
|open24h       |string|Preço unitário de abertura de negociação das últimas 24 horas|
|high24h       |string|Maior preço unitário de negociação das últimas 24 horas      |
|low24h        |string|Menor preço unitário de negociação das últimas 24 horas      |
|baseVolume24h |string|Volume de negociação na moeda de base das últimas 24 horas   |
|quoteVolume24h|string|Volume de negociação na moeda de cotação das últimas 24 horas|
|timestamp     |number|A hora atual do sistema ajustada para o horário UTC          |


Subscrever dados de profundidade
--------------------------------

Após a subscrição, o sistema enviará dados de profundidade dos paress de negociação definidos uma vez por segundo.

```
from novadax import WebSocketClient

nova_client = WebSocketClient('your access key(optional)', 'your secret key(optional)')

def depth_handler(depth):
    print('received depth: ', depth)

nova_client.sub_market_depth("BTC_BRL", depth_handler)

```


> Valor de retorno

```
{
    "asks": [
        ["43687.16", "0.5194"],
        ["43687.2", "1.3129"]
    ],
    "bids": [
        ["43657.57", "0.6135"],
        ["43657.46", "0.0559"]
    ],
    "timestamp": 1565057338020
}

```


### Tópico de subscrição

`MARKET.{{symbol}}.DEPTH.LEVEL0`

### Parâmetro de requisição


|Campo |Mandatório|Tipo  |Detalhes                                          |
|------|----------|------|--------------------------------------------------|
|symbol|true      |string|Símbolo da negociação                             |
|limit |false     |number|O número de compras e vendas a retornar; máximo 20|


### Detalhes de resposta


|Campo    |Tipo  |Detalhes                                           |
|---------|------|---------------------------------------------------|
|asks     |array |Lista de ofertas de venda                          |
|asks[][0]|string|Preço de venda                                     |
|asks[][1]|string|Quantidade de venda                                |
|bids     |array |Lista de ofertas de compra                         |
|bids[][0]|string|Preço de compra                                    |
|bids[][1]|string|Quantidade de compra                               |
|timestamp|number|A hora atual do sistema ajustada para o horário UTC|


Subscrever dados de execução de ordens
--------------------------------------

Após a subscrição, o sistema enviará notificações sobre as ordens recém-executadas.

```
from novadax import WebSocketClient

nova_client = WebSocketClient('your access key(optional)', 'your secret key(optional)')

def trade_handler(trades):
    print('received trade: ', trades)

nova_client.sub_market_trade("BTC_BRL", trade_handler)

```


> Valor de retorno

```
[
    {
        "price": "43657.57",
        "amount": "1",
        "side": "SELL",
        "timestamp": 1565007823401
    },
    {
        "price": "43687.16",
        "amount": "0.071",
        "side": "BUY",
        "timestamp": 1565007198261
    }
]

```


### Tópico de subscrição

`MARKET.{{symbol}}.TRADE`

### Parâmetro de requisição


|Campo |Mandatório|Tipo  |Detalhes             |
|------|----------|------|---------------------|
|symbol|true      |string|Símbolo da negociação|


### Detalhes de resposta


|Campo    |Tipo  |Detalhes                                        |
|---------|------|------------------------------------------------|
|price    |string|Preço unitário da negociação na moeda de cotação|
|amount   |string|Quantidade da negociação na moeda de base       |
|side     |string|A ponta executora da negociação (SELL ou BUY)   |
|timestamp|number|Hora em que ordem foi executada                 |
