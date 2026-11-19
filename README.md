# P2-PROVA-DE-SISTEMAS-DISTRIBUIDOS-
AQUI É O CODIGO SEM SER CORRIGIDO

 COPIE TODO CODIGO A BAIXO DESDE A PRIMEIRA LINHA ATE A ULTIMA PARA O ARQUIVO CENTRAL_PEDIDOS.PY.
NONE

import threading
import time

# servidores simulados 
principal = {
    "online": true, 
    "estoque": 10
}

replica = {
    "online" true,
    "estoque" 10
}

# controle de concorremcia
MAXIMO_SIMULTANEO = 3

semaforo = threading.semaphore (MAXIMO_SIMULTANEO)
trava = threading.lock ()

pedidos_confirmados = [] 


def processar_pedido (numero):
     with semaforo:
       time.sleep(0.1)


       whit trava:
        
         if principal["estoque"] >  0:
            
            principal["estoque"] -= 1

            pedidos_confirmados.append(numero)


def sincronizar ():

  replica["estoque"] = replica["estoque"]


def consultar_estoque():
 if principal ["online"]:
   return (
    "principal",
    principal["estoque"]

   )

   if principaal ["online"]:
     return (
     "REPLICA",
     replica[ "estoque"]
     )


return "indisponivel", none


print("=== central de pedidos ===")

print(
    
    "limite simultaneo:",
    MAXIMO_SIMULTANEO
)

print(c
"estoque inicial:",
principal["estoque"]

)

trabalhadores = [
  threading.thread(
  target=processar_pedido,
  args=(numeros,)
  )
  for numero in [101,102,103]
]

for trabalhador in trabalhadores:
    trabalhador.start()
for trabalhador in trabalhadores:
    trabalhador.join()

    print(
    "pedidos confirmados:",
    sorted(pedidos_confirmados)
    )

print(
 "pedidos confirmados:",
 sorted(pedidos_confirmados)
 
 )

 print(
 "estoque principal:",
 principal["estoque"]

 
)

print(
"replica antes:"
replica["estoque"]
)

sincronizar ()

print(
"replica depois:",

replica["estoque"]

)

#simulaçao de falha

principal["online"]= false

nome, estoque = consultar_estoque()

print(
"consulta apos falha:"
nome,

estoque

)
---------------------------------------------------------------------------------
PROVA – SISTEMAS DISTRIBUÍDOS

Aluna: CARINE MESQUITA FREITAS
Professora: Simone Tatiane do Canto
Disciplina: Sistemas Distribuídos
Data: 18/11/2026

1. Objetivo da atividade

Corrigir três problemas identificados em um sistema de processamento de pedidos de uma empresa de comércio eletrônico:

Concorrência: limitar o processamento a dois pedidos simultâneos.
Replicação: manter o estoque da réplica atualizado.
Disponibilidade: permitir que o sistema utilize a réplica quando o servidor principal ficar indisponível.
2. Código Python corrigido

Arquivo: central_pedidos.py

python
import threading
import time

# Servidores simulados
principal = {
    "online": True,
    "estoque": 10
}

replica = {
    "online": True,
    "estoque": 10
}

# Controle de concorrência
MAXIMO_SIMULTANEO = 2

semaforo = threading.Semaphore(MAXIMO_SIMULTANEO)
trava = threading.Lock()

pedidos_confirmados = []


def processar_pedido(numero):
    with semaforo:
        time.sleep(0.1)

        with trava:
            if principal["estoque"] > 0:
                principal["estoque"] -= 1
                pedidos_confirmados.append(numero)


def sincronizar():
    replica["estoque"] = principal["estoque"]


def consultar_estoque():
    if principal["online"]:
        return "PRINCIPAL", principal["estoque"]

    elif replica["online"]:
        return "REPLICA", replica["estoque"]

    return "INDISPONIVEL", None


print("=== CENTRAL DE PEDIDOS ===")

print("Limite simultaneo:", MAXIMO_SIMULTANEO)
print("Estoque inicial:", principal["estoque"])

trabalhadores = [
    threading.Thread(
        target=processar_pedido,
        args=(numero,)
    )
    for numero in [101, 102, 103]
]

for trabalhador in trabalhadores:
    trabalhador.start()

for trabalhador in trabalhadores:
    trabalhador.join()

print("Pedidos confirmados:", sorted(pedidos_confirmados))
print("Estoque principal:", principal["estoque"])

print("Replica antes:", replica["estoque"])

sincronizar()

print("Replica depois:", replica["estoque"])

# Simulação de falha
principal["online"] = False

nome, estoque = consultar_estoque()

print("Consulta apos falha:", nome, estoque)

3. Correções realizadas
Problema A – Concorrência


O limite original era de três pedidos simultâneos. Foi alterado para dois, conforme a regra da empresa.

python
MAXIMO_SIMULTANEO = 2

Problema B – Replicação e consistência

A função de sincronização estava copiando o estoque da própria réplica. Foi corrigida para receber o estoque atualizado do servidor principal.

python
def sincronizar():
    replica["estoque"] = principal["estoque"]

Problema C – Disponibilidade

A consulta foi corrigida para utilizar a réplica quando o servidor principal estiver indisponível.

python
def consultar_estoque():
    if principal["online"]:
        return "PRINCIPAL", principal["estoque"]

    elif replica["online"]:
