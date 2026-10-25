# Casos de teste gerados

Os casos sao especificacoes candidatas derivadas dos prompts. Os resultados esperados dependem das premissas do README; nenhum caso foi executado contra uma aplicacao.

## Prompt 1: contrato e cobertura de respostas

| ID | Cenario e passos resumidos | Resultado esperado |
| --- | --- | --- |
| P1-01 | Com horario livre, enviar `POST /appointments` com os tres campos validos. | `201`; resposta contem id; consulta posterior retorna o mesmo agendamento; uma reserva existe. |
| P1-02 | Enviar corpo sem `customer_id`. | `400`; nenhuma reserva e criada. |
| P1-03 | Enviar `starts_at` em formato invalido. | `400`; nenhuma reserva e criada. |
| P1-04 | Reservar um horario ja ocupado por outro agendamento. | `409`; reserva original permanece; nao ha segunda reserva. |
| P1-05 | Consultar com `GET /appointments/{id}` o id retornado em P1-01. | `200`; id, cliente, servico e horario correspondem aos dados enviados. |
| P1-06 | Consultar `GET /appointments/{id}` com id inexistente. | `404`; nenhuma alteracao no banco. |
| P1-07 | Cancelar o agendamento criado e consultar o registro. | `204`; registro fica cancelado e o horario volta a estar disponivel. |
| P1-08 | Criar um agendamento e, em seguida, tentar reservar o mesmo horario. | Primeira chamada `201`, segunda `409`; apenas uma reserva ativa. |

## Prompt 2: estado compartilhado e falhas entre componentes

| ID | Cenario e passos resumidos | Resultado esperado |
| --- | --- | --- |
| P2-01 | Criar um agendamento e inspecionar a fila de notificacao. | Existe um `AppointmentCreated` correspondente ao id persistido; nenhum evento duplicado. |
| P2-02 | Enviar requisicao invalida e inspecionar banco, servico de disponibilidade e fila. | `400`; nenhum componente registra reserva ou evento. |
| P2-03 | Tentar reservar horario ocupado e observar banco e fila. | `409`; nao aparece novo agendamento nem `AppointmentCreated`. |
| P2-04 | Cancelar agendamento e tentar reservar novamente o mesmo horario. | Cancelamento `204`; horario torna-se reservavel; evento `AppointmentCancelled` corresponde ao agendamento cancelado. |
| P2-05 | Enviar duas requisicoes concorrentes para o mesmo horario livre. | Exatamente uma tem sucesso; a outra recebe conflito; existe uma unica reserva ativa. |
| P2-06 | Simular indisponibilidade da fila apos o commit de uma criacao. | Definir se a API retorna sucesso, como o evento sera recuperado e como evitar duplicatas. |
| P2-07 | Repetir a mesma requisicao de criacao apos timeout do cliente. | Regra nao especificada: decidir se existe chave de idempotencia e qual resposta/estado e esperado. |
| P2-08 | Observar a ordem entre persistencia e publicacao do evento. | O evento so pode ser consumido depois de o agendamento estar confirmado; definir a estrategia para evitar evento perdido. |

## Observacoes para implementacao

- Cada teste deve preparar e limpar seu proprio estado, sem depender da ordem da tabela.
- Verificacoes diretas no banco e na fila devem usar recursos de teste isolados, nao dados de producao.
- P2-06 a P2-08 sao cenarios de descoberta de requisitos: ainda nao possuem um oraculo completo. Resolver a ambiguidade antes de automatizar.
- Para P2-05, usar barreira ou sincronizacao para iniciar as requisicoes em paralelo; uma sequencia de chamadas nao testa concorrencia.
