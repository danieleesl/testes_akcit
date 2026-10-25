# Analise dos resultados

## Influencia de cada tipo de prompt

O prompt guiado pelo contrato trouxe mais amplitude funcional: respostas de sucesso e erro, validacao de entrada, conflito, consulta, cancelamento e consistencia basica do estado. Seu principal limite e depender do que esta escrito no contrato. So com esse prompt, uma resposta `201` poderia parecer correta mesmo que a API nao persistisse a reserva ou publicasse uma notificacao incorreta.

O prompt voltado a interacoes entre componentes trouxe mais profundidade: verificacao da fila e do banco, ausencia de efeitos parciais, concorrencia e ordenacao entre commit e evento. Tambem ajudou a revelar lacunas de especificacao. Em contrapartida, pode sugerir comportamento ainda nao definido, como o tratamento de timeout e idempotencia.

Em conjunto, os prompts foram mais uteis do que qualquer um isoladamente: o primeiro organiza cobertura do contrato; o segundo investiga consistencia e falhas de integracao. Os casos nao foram executados em uma API; esta analise compara utilidade e variedade dos casos gerados, nao taxa de aprovacao nem defeitos encontrados.

## Ajustes necessarios

- Delimitar endpoints, campos e codigos HTTP para reduzir a invencao de contrato.
- Pedir pre-condicoes, passos, observacoes e oraculo esperado para tornar cada caso verificavel.
- Exigir verificacao do estado persistido e de eventos para ir alem das respostas HTTP.
- Orientar a LLM a marcar regras ausentes como perguntas, especialmente para idempotencia e falha da fila.
- Especificar banco e fila isolados e dados ficticios para evitar dependencias externas.

## Reflexao

A GenAI funciona como copiloto para ampliar caminhos de teste, mas nao substitui a validacao do contrato nem decisoes arquiteturais. O resultado deve ser revisado por quem conhece o sistema, transformado em testes executaveis e rodado no ambiente de integracao. Um caso bem escrito ainda pode estar errado se o comportamento esperado tiver sido assumido pelo modelo.
