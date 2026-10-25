# Prompts para testes de integracao com GenAI

Material da Unidade III sobre testes de integracao entre uma API de agendamento, o servico de disponibilidade, o banco de dados e a fila de notificacoes.

> Cenario didatico: os endpoints e comportamentos abaixo sao premissas criadas para a atividade. Os casos foram elaborados com apoio de uma LLM, mas nao foram executados contra uma API implementada.

## Cenario

Ao solicitar um agendamento, a API consulta a disponibilidade, registra a reserva e publica um evento para notificacao. Ao cancelar, deve atualizar o registro, liberar o horario e publicar o evento correspondente. O foco dos testes e verificar a comunicacao entre componentes e os efeitos observaveis depois de cada operacao.

## Premissas do contrato

- `POST /appointments` recebe `customer_id`, `service_id` e `starts_at`; retorna `201 Created` com o agendamento quando o horario esta disponivel.
- Horario indisponivel retorna `409 Conflict` e nao cria reserva.
- Corpo invalido retorna `400 Bad Request` e nao altera estado.
- `GET /appointments/{id}` retorna `200 OK` para agendamento existente e `404 Not Found` para id inexistente.
- `DELETE /appointments/{id}` cancela o agendamento e retorna `204 No Content`; o horario volta a ficar disponivel.
- A notificacao e publicada somente depois da confirmacao da operacao no banco de dados.
- Falha na publicacao da notificacao deve ser recuperavel por mecanismo de retentativa, sem desfazer uma reserva confirmada. Essa premissa exige uma decisao arquitetural antes de virar criterio definitivo.
- Os testes usam banco e fila isolados, dados ficticios e um relogio controlavel. Nao chamam servicos externos reais.

## Conteudo

- [Prompts utilizados](docs/prompts.md)
- [Casos gerados](docs/casos.md)
- [Analise dos resultados](docs/analise.md)
- [Texto para o forum](docs/postagem-forum.md)

## Publicacao

Crie um repositorio publico no GitHub, por exemplo `prompts-testes-integracao-agendamento-genai`, envie estes arquivos e substitua no texto do forum o campo `[LINK DO REPOSITORIO]` pelo endereco criado. A publicacao remota nao foi realizada por este ambiente.