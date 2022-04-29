<h1 align="center">
  SHEEPPY
</h1>

## Login


## Logado

* Depois de logado você foi cadastrado em algumas listas onde receberá menssagem periodicamente como:
```
"all-open-_website" -> Mostra gatilhos de entradas de estrategias
"wb_extesion_result" -> Resultado dos gatilhos de entradas das estrategias
"all-coins'", -> Moedas abertas
"all-open-strategies" -> Cards referente ao website
"all-open-candle" -> Ultimo cande do ativo
"candle-database" -> últimos candles do ativo
``` 
* all-open-_website
Mostra todos os gatilhos de estratégias que serão executadas ao longo do dia, Cada estratégia tem seu período de ser, algumas de 5 em 5 minutos outras a cada 30 minutos e algumas a cada uma hora.

* wb_extesion_result
Você consegue visualizar todos os resultados, que cada gatilho teve.

* all-coins
Acada 60 segundos você deve receber um novo par, de ativos que se encontram abertos na, corretora iqoption


* all-open-strategies
Quando logado o valor default desse end point aponta para,
Todos os ativos em M5 com 2 De martigale assim recebendo uma nova lista e exatos 5 minutos. 

* all-open-candle
Recebe O ultimo candle de um determinado Ativo a cada 1 segundo.
Por padrão quando feito login ele e sempre selecionado em
EUR-USD|EUR-USD-OTC M5.

* candle-database
Recebe vários candles fechados, de ter terminado ativo.
Por padrão quando feito login ele e sempre selecionado em
EUR-USD|EUR-USD-OTC M5.

* Noticias| Tendencias
Por padrão quando feito login ele e sempre selecionado em
EUR-USD|EUR-USD-OTC M5.


* Observação
`isso foi feito pois notei que na sua aplicação sempre que feito o auto reload, ou F5, ele sempre volta para o mesmo ativo EURUSD, então feito dessa forma ele não precisa fazer request`

* Delay do login
Atualmente não existe delay de login, chegando a ser instante o
Pode demorar ate 1 segundo para informação do ultimo candle aparecer


# Endpoints

## all-coins

Mostra todas as Moedas ativas no sistema naquele momento, são atualizadas a cada 60 segundos
```json
Payload
{
    'name': 'all-coins',
}
```


```json
Response:
{
    "instance": "coins",
    "ok": "true",
    "message": [
        "GBPJPY-OTC",
        "USDJPY-OTC",
        "GBPUSD-OTC",
        "USDCHF-OTC",
        "EURGBP-OTC",
        "EURUSD-OTC",
        "AUDCAD-OTC",
        "EURJPY-OTC",
        "NZDUSD-OTC",
        "USDINR-OTC"
    ]
}
```

## Tendencias

Tendencias são sempre enviadas a cada 60 segundos, com base no ativo que você escolheu como ultimo candle, não tem outra maneira de ativação.


```json
{
    "instance": "trend",
    "active": "EURUSD",
    "timeframe": "M5",
    "trend": "Tendencia de alta",
    "ok": "true"
},

{
    "instance": "trend",
    "active": "EURUSD",
    "timeframe": "M5",
    "trend": "Tendencia de baixa",
    "ok": "true"
},

{
    "instance": "trend",
    "active": "EURUSD",
    "timeframe": "M5",
    "trend": "Tendencia nula",
    "ok": "true"
}
```

## Noticias

Noticias São enviadas acada 60 segundos, isso e quando elas estiverem disponivel, uma forma de saber se tem noticia naquele horario e consultando uma api REST: `https://api.agbot.com.br/api/noticias/`
Se encontrada com `{}` significa que não tem noticias nesse horário, mas você não precisa  ficar consultando a API REST quando tiver uma noticia no ativo que está selecionado o ultimo candle ele deve te informa com algo assim:

```json
// WEBSCOKET
{'instance': 'news', 'active': EURUSD, 'priority': 2, 'hour': 14:00:00,'ok': 'true'}
```

```json
// API REST - Sem noticias
{
"ok": {}
}

// API REST - Com noticias
{
"ok": {
    "USD": [2]
    }
}
```
## all-open-candle

Retorna O ultimo candle em movimento de determinado ativo, aparece somente um candle correspondente ao seu timeframe, a cada 1 segundo.

```json
Parâmetros:
close -> Fechamento do candle
open  -> Abertura do Candle
min   -> Mínimo que o candle chegou
max   -> Máximo que o candle chegou
start -> Hora que o candle naceu
at    -> Hora de envio do Candle
delay -> Tempo de Delay ate o envio do Candle
``` 

```json
Payload
{
    "name": "all-open-candle",
    "active": "EURUSD",
    "timeframe": "M1",
}
```

```json
Response
{
    "instance": "all-open-candle",
    "active": "GBPJPY-OTC",
    "timeframe": "M1",
    "M1": {
        "name": "GBPJPY-OTC",
        "close": 132.3759,
        "open": 132.3807,
        "min": 132.3756,
        "max": 132.3807,
        "at": 1651187501,
        "start": 1651187460,
        "delay": 1.034128189086914
    }
}

```


## candle-database
Retorna as ultimas 72Horas de candle de determinado ativo.
```json
Parametros:
close -> Fechamento do candle
open  -> Abertura do Candle
min   -> Minimo que o candle chegou
max   -> Maximo que o candle chegou
start -> Hora que o candle naceu
at    -> Hora de envio do Candle
``` 
```json
Payload
{     
    "name": "candle-database",
    "msg": {
        "active": "EURUSD",
        "timeframe": "M1"},
}
```

```json
Response
{
    "instance": "signal",
    "active": "GBPJPY-OTC",
    "timeframe": "M1",
    "ok": "true",
    "message": [
        {
            "at": 1649448240,
            "start": 1649448180,
            "end": 1649448240,
            "open": 138.3695,
            "max": 138.3774,
            "min": 138.3642,
            "close": 138.3743,
            "volume": 0
        },...

```

## all-open-strategies
Retorna a estrutura de dados usado nos Cards do AGBot,
Vale lembrar que a ordem de index seguem um padrão por Exemplo: `ciclos[0] - > time[0] -> direction[0] -> date[0]`
Correspondem a mesma ordem o `index 0` de todas as chaves dentro do dicionário são o mesmo item
```json
Parametros                            Validos
name       -> Nome do Ativo        | ou pode usar all no lugar
timeframe  -> Tempo de abertura    | M1 M5 or M15
gale       -> Quantidade de Gales  | G0 G1 or G2
candle     -> Quantidade de Candles| 24 48 or 92
``` 
```json
Payload
{     
    "name": "all-open-strategies",
    "msg": {
        "name": "EURUSD",
        "timeframe": "M1",
        "gale": "G1",
        "candle": 24,}
}
```

```json
response
"instance": "strategie",
    "ok": "true",
    "message": {
        "ok": {
            "MHI": [
                {
                    "name": "AG-MVP",
                    "active": "EUR/USD",
                    "timeframe": "M1",
                    "WIN": 15,
                    "winG1": 6,
                    "winG2": 0,
                    "loss": 2,
                    "ciclos": [
                        "WIN",
                        "WIN",
                        "D",
                        "WIN",
                        "WIN",
                        "G1",
                        "LOSS",
                        "WIN",
                        "G1",
                        "WIN",
                        "G1",
                        "G1",
                        "WIN",
                        "WIN",
                        "WIN",
                        "WIN",
                        "WIN",
                        "LOSS",
                        "WIN",
                        "G1",
                        "G1",
                        "WIN",
                        "WIN",
                        "WIN"
                    ],
                    "time": [
                        "23:25",
                        "23:20",
                        "23:16",
                        "23:10",
                        "23:05",
                        "23:01",
                        "22:56",
                        "22:50",
                        "22:46",
                        "22:40",
                        "22:36",
                        "22:31",
                        "22:25",
                        "22:20",
                        "22:15",
                        "22:10",
                        "22:05",
                        "22:01",
                        "21:55",
                        "21:51",
                        "21:46",
                        "21:40",
                        "21:35",
                        "21:30"
                    ],
                    "direction": [
                        "CALL",
                        "PUT",
                        "CALL",
                        "CALL",
                        "PUT",
                        "CALL",
                        "CALL",
                        "PUT",
                        "CALL",
                        "CALL",
                        "PUT",
                        "PUT",
                        "CALL",
                        "PUT",
                        "PUT",
                        "CALL",
                        "PUT",
                        "PUT",
                        "CALL",
                        "PUT",
                        "CALL",
                        "PUT",
                        "CALL",
                        "CALL"
                    ],
                    "date": [
                        1651199100.0,
                        1651198800.0,
                        1651198560.0,
                        1651198200.0,
                        1651197900.0,
                        1651197660.0,
                        1651197360.0,
                        1651197000.0,
                        1651196760.0,
                        1651196400.0,
                        1651196160.0,
                        1651195860.0,
                        1651195500.0,
                        1651195200.0,
                        1651194900.0,
                        1651194600.0,
                        1651194300.0,
                        1651194060.0,
                        1651193700.0,
                        1651193460.0,
                        1651193160.0,
                        1651192800.0,
                        1651192500.0,
                        1651192200.0
                    ]
                }
            ]
```


## wb_extesion_result
Recebe os resultados depois que os gatilhos de entradas são enviados
```json
    "type"       -> tipo identificador
    "active":    -> ativo
    "timeframe": -> timeframe
    "STRG":      -> nome da estratégia
    "win":       -> se for ganho | true or false
    "doji":      -> se for for empate | true or false
    "loss":      -> se for perca | true or false
    "gale":      -> quantidade de martifales
    "direction": -> Direção do ativo| Call or PUT
    "hour":      -> Hora que começou
    "timestamp": -> timestamp da hora
}
```

```json
Payload
{     
    "name": "extension-result",
}
```

```json
response
{
    "type": "db",
    "active": "EURJPY",
    "timeframe": 1,
    "STRG": "MHI3",
    "win": true,
    "doji": false,
    "loss": false,
    "gale": 0,
    "direction": "CALL",
    "hour": "23:37",
    "timestamp": 1651199820
}
```


## all-open-website
Cada estratégia tem seu tempo de ser executado, então o envio dessa informação varia muito, mas com toda certeza a cada 5 minutos você
alguns resultados, quanto maior o time frame: M1 M5 M15 mais tempo demora pra ela retornar, lembrando que M15 a mensagem aparece a mais o menos achada 1H.

```
MHI M1 ENTRADAS SEMPRE COM O 5 OU 00 EXEMPLO: 10:00 OU 10:05 OU 10:10
MHI M5 ENTRADAS SEMPRE COM 00 OU 30 EXEMPLO 10:30 OU 11:00
MHI M15 ENTRADAS SEMPRE COM 00 EXEMPLO: 11:00 OU 12:00
AG3 M1 ENTRADAS SEMPRE COM 4 OU 9 EXEMPLO 10:04 OU 10:09
ALFA 15 M15 ENTRADAS SEMPRE COM 15 EXEMPLO 10:15 OU 11:15
FIVE FLIP M5 ENTRADAS SEMPRE COM 25 OU 55 EXEMPLO 10:25 OU 10:55
GARRA E MOSQUETEIROS M5 ENTRADAS SEMPRE COM 10,25,40 OU 55 EXEMPLO 10:10, 10:25, 10:40 OU 10:55
MILHAO MAIORIA E MINORIA M5 ENTRADAS SEMPRE COM 30 OU 00 EXEMPLO 10:00 OU 10:30
TURNER OVER M15 ENTRADAS SEMPRE COM  00 EXEMPLO 10:00 OU 11:00 OU 12:00
SEVEN FLIP M1 ENTRADAS SEMPRE COM  FINAL 6  EXEMPLO 10:06 10:16 10:26 10:36
TORRE GEMEAS M5 ENTRADAS SEMPRE COM 25 OU 55 EXEMPLO 10:25 10:55
3 VIZINHOS M5 ENTRADAS SEMPRE COM 15 OU 45 EXEMPLO 10:15 10:45
```


## timer_website
Informa Quanto tempo para Próxima Estratégia entrar em vigor
São enviadas a cada 1 segundos,

```json
// timer -> tempo em segundos
{'instance': 'timer', 'strategies': AG-MVP, 'timer':300,'ok': 'true'}
```