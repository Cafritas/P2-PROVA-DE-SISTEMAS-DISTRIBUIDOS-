# P2 - Sistemas Distribuídos

**Aluno(a):** Carine Mesquita Freitas
**Professora:** Simone Tatiane do Canto
**Data:** 18/11/2026

## 1. Situação-problema

A Central de Pedidos Distribuída tem um servidor principal e uma réplica que guarda uma cópia do estoque. A equipe de TI encontrou três problemas: (A) o sistema aceita 3 pedidos em processamento ao mesmo tempo, mas a empresa permite apenas 2; (B) o estoque da réplica não é atualizado depois dos pedidos; (C) quando o servidor principal fica indisponível, o sistema não consegue usar a réplica para responder às consultas.

## 2. Primeira execução

Ao executar o código original, sem nenhuma correção, o terminal mostrou: limite simultâneo 3, estoque inicial 10, pedidos confirmados [101, 102, 103], estoque principal 7, réplica antes 10 e réplica depois 10 (não mudou). Após a simulação de falha, a consulta retornou `INDISPONIVEL None`. Ou seja, os três problemas apareceram na prática.

### Evidência 1 - Primeira execução

![Evidência 1](evidencias/evidencia1.png)

## 3. Problema A - Concorrência

Qual problema foi identificado?

A variável `MAXIMO_SIMULTANEO` estava com valor 3, que é o valor usado para criar o semáforo (`threading.Semaphore(MAXIMO_SIMULTANEO)`). Assim, até 3 pedidos podiam ser processados ao mesmo tempo, violando a regra da empresa de no máximo 2.

Qual alteração foi realizada?

Alterei `MAXIMO_SIMULTANEO = 3` para `MAXIMO_SIMULTANEO = 2`. Como o semáforo é criado a partir dessa variável, agora só 2 pedidos entram na região de processamento ao mesmo tempo; o terceiro espera um dos outros terminar.

### Evidência 2 - Concorrência

![Evidência 2](evidencias/evidencia2.png)

## 4. Problema B - Replicação e consistência

Qual problema foi identificado?

A função `sincronizar()` fazia `replica["estoque"] = replica["estoque"]`, ou seja, a réplica copiava o valor dela mesma e nunca recebia o valor do servidor principal. Por isso a réplica ficava com 10 enquanto o principal já estava com 7 (inconsistência).

Qual alteração foi realizada?

Troquei a instrução para `replica["estoque"] = principal["estoque"]`. Agora, ao sincronizar, a réplica copia o estoque atual do servidor principal e passa a mostrar 7.

### Evidência 3 - Sincronização

![Evidência 3](evidencias/evidencia3.png)

## 5. Problema C - Tolerância a falhas

Qual problema foi identificado?

Na função `consultar_estoque()`, a segunda condição verificava `principal["online"]` de novo, em vez de `replica["online"]`. Quando o principal ficava offline, as duas condições davam falso e a função retornava `INDISPONIVEL`, mesmo com a réplica funcionando.

Qual alteração foi realizada?

Mudei a segunda condição para `if replica["online"]:`. Agora, com o principal offline, o sistema consulta a réplica e retorna `REPLICA 7`, continuando a responder.

### Evidência 4 - Tolerância a falhas

![Evidência 4](evidencias/evidencia4.png)

## 6. Validação do programa

Depois das três correções, o programa executou sem erros e mostrou: limite simultâneo 2 (de acordo com a regra), os três pedidos confirmados [101, 102, 103], estoque principal 7, réplica 10 antes e 7 depois da sincronização (consistente) e, após a falha simulada do principal, a consulta retornou `REPLICA 7`, ou seja, o sistema continuou funcionando.

### Evidência 5 - Execução completa

![Evidência 5](evidencias/evidencia5.png)

## 7. Análise técnica

### Questão 1
Qual é a função do semáforo no processamento dos pedidos?

Resposta:

O semáforo controla quantos pedidos podem ser processados ao mesmo tempo. Ele funciona como um contador de "vagas": cada pedido pega uma vaga ao entrar no bloco `with semaforo` e a devolve ao sair. Quando as vagas acabam, os demais pedidos esperam. Assim o sistema respeita o limite da empresa (2 simultâneos) e não sobrecarrega o servidor.

### Questão 2
Por que é necessário sincronizar os dados entre o servidor principal e a réplica?

Resposta:

Porque a réplica existe para ser uma cópia fiel do principal. Se ela não for atualizada, fica com dados antigos (inconsistência) e, se o principal cair, o sistema passaria a informar um estoque errado (10 em vez de 7), o que pode gerar vendas de produtos que não existem. A sincronização mantém a consistência entre os servidores.

### Questão 3
O que significa tolerância a falhas e como ela aparece neste programa?

Resposta:

Tolerância a falhas é a capacidade de o sistema continuar funcionando mesmo quando um componente falha. No programa, ela aparece na função `consultar_estoque()`: se o servidor principal fica offline (simulado com `principal["online"] = False`), a consulta é redirecionada para a réplica e continua retornando o estoque, em vez de ficar indisponível.

### Questão 4
Para que serve o Lock no programa e como o controle de acesso aos recursos ajuda a prevenir problemas de concorrência?

Resposta:

O Lock garante exclusão mútua: só uma thread por vez executa o bloco `with trava`, onde o estoque é verificado e diminuído e o pedido é registrado. Sem ele, duas threads poderiam ler o mesmo valor de estoque ao mesmo tempo e gravar um resultado errado (condição de corrida), perdendo atualizações ou vendendo além do estoque. O Lock protege os dados compartilhados, mantendo-os corretos.

### Questão 5
A sincronização do estoque deve acontecer antes ou depois do processamento dos pedidos? Explique a relação de causalidade.

Resposta:

Deve acontecer depois. O estoque do principal muda como consequência do processamento dos pedidos (causa: pedidos processados; efeito: estoque 10 → 7). A réplica só pode copiar o valor correto depois que essa mudança ocorreu. Se sincronizar antes, ela copiaria o valor antigo (10) e voltaria a ficar desatualizada. Por isso a ordem correta é processar os pedidos primeiro e sincronizar em seguida.

## 8. Conclusão

Nesta atividade aprendi na prática como funcionam concorrência, replicação, consistência e tolerância a falhas em um sistema distribuído. Vi que o semáforo limita o processamento simultâneo e que o Lock protege os dados compartilhados. Também entendi que a réplica precisa ser sincronizada depois das alterações no principal e que ela permite ao sistema continuar funcionando se o servidor principal falhar. Pequenos erros no código (um número, uma variável) causam problemas sérios, por isso é importante ler e testar com atenção.

## 9. Entrega

**Aluno(a):** Carine Mesquita Freitas
**Repositório:** [LINK DO GITHUB]
**Data:** 18/11/2026
