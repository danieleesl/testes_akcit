# Texto para postagem no forum

Escolhi como cenario a integracao de uma API de agendamento com um servico de disponibilidade, um banco de dados e uma fila de notificacoes. Na criacao, a API consulta o horario, persiste a reserva e publica um evento; no cancelamento, atualiza o agendamento, libera o horario e publica outro evento. O contrato usado e didatico, criado para esta atividade.

Usei dois tipos de prompt com uma LLM. O primeiro foi guiado pelo contrato da API e pediu casos positivos, negativos e de limite, com pre-condicoes, chamadas e verificacao do estado persistido. Ele gerou boa cobertura funcional, incluindo sucesso, dados invalidos, conflito de horario, consulta e cancelamento. Seu limite e nao encontrar problemas que nao estejam descritos no contrato.

O segundo prompt focou nas interacoes entre componentes e em falhas: consistencia entre banco e fila, ausencia de efeitos parciais, concorrencia e retentativas. Ele trouxe casos mais profundos, como duas requisicoes disputando o mesmo horario, e apontou regras ainda indefinidas. Idempotencia e comportamento quando a fila falha precisam ser decididos pela equipe antes de se tornarem criterios de teste.

Os ajustes mais importantes foram fornecer os componentes e as regras explicitamente, exigir passos e resultados observaveis, pedir que a LLM nao inventasse endpoints ou codigos HTTP e orientar que ambiguidades fossem listadas como perguntas. Assim, os casos ficaram mais verificaveis e menos sujeitos a suposicoes.

Repositorio com prompts, casos e analise: https://github.com/danieleesl/testes_akcit

Este material registra casos especificados com apoio de LLM; nao os apresento como testes automatizados executados, pois nao ha uma API implementada neste exemplo. Para mim, a GenAI ajuda a ampliar a suite, mas a revisao humana e a execucao continuam necessarias para confirmar se os testes representam o comportamento real do sistema.
