# Prompts utilizados

Os prompts abaixo foram usados para gerar uma especificacao de casos para o cenario didatico descrito no README. As premissas sao parte do contexto, nao um contrato de uma API existente.

## Prompt 1: cenarios guiados pelo contrato

```text
Atue como pessoa engenheira de qualidade. Gere casos de teste de integracao para a API didatica de agendamento descrita abaixo.

Contrato:
- POST /appointments recebe customer_id, service_id e starts_at. Se o horario estiver disponivel, retorna 201 e cria um agendamento.
- Horario indisponivel retorna 409 e nao cria reserva.
- Corpo invalido retorna 400 e nao altera estado.
- GET /appointments/{id} retorna 200 para registro existente e 404 para id inexistente.
- DELETE /appointments/{id} retorna 204, cancela o registro e libera o horario.
- A reserva confirmada deve estar persistida e visivel em uma consulta posterior.

Gere casos positivos, negativos e de limite. Para cada caso, informe: identificador, objetivo, pre-condicoes, passos HTTP, status e corpo esperados, e verificacao do estado persistido. Inclua dados ficticios concretos. Nao invente endpoints, campos, codigos HTTP ou regras que nao estejam no contrato. Identifique qualquer ambiguidade como pergunta, em vez de assumir uma resposta.
```

## Prompt 2: interacoes, falhas e consistencia entre componentes

```text
Atue como especialista em testes de integracao distribuida. Para o fluxo de agendamento descrito, procure falhas que nao seriam detectadas verificando somente o status HTTP.

Fluxo: POST /appointments consulta o servico de disponibilidade, persiste o agendamento e publica AppointmentCreated na fila depois do commit. DELETE /appointments/{id} cancela o agendamento, libera o horario e publica AppointmentCancelled depois do commit. O ambiente de teste tem banco e fila isolados.

Produza casos para verificar: efeito de cada operacao no componente seguinte; ausencia de efeitos parciais em validacao ou conflito; repeticao de requisicao; concorrencia pelo mesmo horario; ordem entre commit e publicacao; e falha/retentativa da fila. Para cada caso, descreva setup, sequencia de chamadas, observacoes no banco/fila e resultado esperado. Separe claramente fatos fornecidos de hipoteses. Se faltar uma regra para definir o resultado, formule uma pergunta e nao invente um oraculo.
```

## Ajustes feitos

- Informar os componentes envolvidos e o que cada um deve observar levou a verificacoes alem do status HTTP.
- Pedir setup, passos e oraculo por caso reduziu respostas vagas que apenas sugeriam "testar concorrencia".
- Proibir a criacao de endpoints e regras nao descritos evitou especificacoes ficticias.
- Solicitar que ambiguidades virem perguntas expos a necessidade de definir idempotencia e a politica para falhas da fila antes de automatizar esses cenarios.
