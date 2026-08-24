# Product Sessions

**App nativo para macOS que transforma testes de usabilidade gravados em relatórios de UX estruturados — 100% processado no dispositivo, sem depender de nuvem.**

---

## O que é

Product Sessions é um app de macOS para quem conduz testes de usabilidade moderados. Ele grava a sessão (microfone + áudio do sistema, capturando moderador e protótipo ao mesmo tempo), transcreve automaticamente e usa IA para gerar um relatório estruturado de UX — com resumo executivo, análise por tarefa, achados com evidência em citações e recomendações de design. Dali, os achados viram itens de ação rastreáveis que podem ser enviados diretamente para o Jira, fechando o ciclo entre pesquisa e execução.

Todo o pipeline — transcrição e análise de IA — roda **no dispositivo**, sem enviar áudio, transcrição ou dados do participante para servidores externos.

---

## O desafio

Sintetizar um teste de usabilidade é um trabalho manual e repetitivo: gravar a sessão, reassistir o vídeo pra encontrar os momentos certos, anotar falas literais como evidência, categorizar problemas, escrever recomendações e, por fim, transformar tudo isso em tarefas para o time de produto — geralmente copiando e colando entre um doc de pesquisa e o board de trabalho.

Além do tempo gasto, existe uma tensão real quando se usa ferramentas de IA nesse processo: sessões de teste contêm falas, comportamentos e às vezes dados sensíveis de participantes reais. Depender de um serviço em nuvem para resumir isso significa abrir mão de controle sobre onde esses dados trafegam e ficam armazenados — um trade-off que nem todo pesquisador, ou toda empresa, está disposto a aceitar.

O objetivo era claro: reduzir o tempo entre "sessão gravada" e "insight acionável", sem sacrificar privacidade nem a qualidade da análise.

---

## A solução

Product Sessions ataca isso com um pipeline único que qualquer origem de dado atravessa — gravação ao vivo, importação de gravação existente ou colagem de uma transcrição — e que sempre termina no mesmo lugar: uma transcrição confiável e um relatório de UX estruturado.

A decisão de produto mais importante foi manter a IA **100% on-device**, usando os Apple Foundation Models (com um mecanismo de fallback por template determinístico para quando o modelo não está disponível no aparelho). Isso significa que a análise roda inteiramente offline, sem custo de API e sem exposição de dados — o trade-off aceito em troca disso foi trabalhar dentro da janela de contexto menor de um modelo local, resolvido com uma estratégia de map-reduce sobre a transcrição.

O relatório gerado não trata a IA como um oráculo: todo achado é redigido como hipótese ("possível problema de encontrabilidade..."), nunca como fato, e vem ancorado em uma citação com timestamp — para que o pesquisador possa verificar, e não só confiar cegamente.

---

## Principais funcionalidades

- **Gravação ao vivo dupla** — captura simultânea de microfone e áudio do sistema, para registrar tanto a voz do participante/moderador quanto o que acontece na tela do protótipo.
- **Importação flexível** — também aceita gravações existentes ou transcrições já prontas (arquivo ou colagem direta).
- **Transcrição on-device** — usando o framework de fala nativo da Apple, sem envio de áudio para fora do aparelho.
- **Motor de análise de IA on-device** — gera um relatório completo: resumo executivo com o "arco" da sessão, leitura de sentimento e momentos emocionais marcantes (com timestamp), análise tarefa a tarefa com confiança de conclusão, achados categorizados por tipo de problema de UX e recomendações de design.
- **Evidência rastreável** — cada achado carrega a citação exata e o momento em que ela ocorreu, mantendo o relatório auditável.
- **Action Board** — quadro estilo kanban (a fazer / em andamento / concluído) para transformar achados e recomendações em tarefas de trabalho, com prioridade sugerida automaticamente por categoria.
- **Integração com Jira** — o ponto em que o insight vira trabalho: qualquer item do Action Board pode ser enviado com um clique para o Jira Cloud, criando um card com contexto completo (produto, teste de origem, categoria, prioridade e evidência formatada), labels automáticas e um link direto de volta pro card criado — evitando duplicidade, já que o item marca que foi sincronizado.
- **App bilíngue** — interface e saída da IA em português e inglês, seguindo automaticamente o idioma do sistema.

---

## Processo de produto

**1. Discovery**
Ponto de partida: entender o que já existe. Usei o Maze — referência de mercado em relatórios de teste de usabilidade com IA — como benchmark de qualidade para os resumos gerados. Isso deixou claro o nível de "completude" que um relatório de IA precisa ter (contexto de áudio, leitura emocional, narrativa da sessão) para ser realmente útil, e não só um resumo genérico.

**2. Definição**
Com o benchmark em mãos, veio a decisão de produto que moldou toda a arquitetura: manter a IA 100% on-device em vez de replicar a abordagem em nuvem do Maze. Privacidade e funcionamento offline pesaram mais do que igualar a profundidade de um modelo em nuvem — e essa escolha se tornou um princípio orientador para todas as features seguintes.

**3. Design da estrutura do relatório**
Antes de qualquer prompt de IA, defini o *modelo de dados* do relatório: achados sempre como hipótese (nunca afirmação), evidência sempre ancorada em citação com timestamp, e confiança de conclusão por tarefa. Essa estrutura existe para gerar confiança — o pesquisador precisa poder verificar o que a IA disse, não só aceitar.

**4. Construção**
Implementação do pipeline único (gravação ao vivo, importação de gravação ou de transcrição convergindo no mesmo fluxo de análise), da transcrição on-device e do motor de análise com estratégia de map-reduce para lidar com a janela de contexto menor de um modelo local — incluindo um fallback por template para dispositivos sem suporte a Apple Intelligence, garantindo que o app nunca trava sem gerar relatório nenhum.

**5. Iteração guiada pelo benchmark**
De volta à comparação com o Maze, mapeei as lacunas do relatório inicial e priorizei: resumo executivo evoluiu para incluir um "arco narrativo" da sessão, e foram adicionados sinais de sentimento e momentos emocionais marcantes — aproximando a leitura "como a sessão se sentiu" que o Maze entrega, mas ancorada em transcrição real, sem dados acústicos armazenados.

**6. Expansão para bilíngue**
Com o núcleo do produto validado, o app foi expandido para funcionar totalmente em português e inglês — inclusive a saída da IA, que passou a escrever no idioma do sistema em vez de sempre em inglês. Decisão de produto aqui: nunca usar hack de sufixo de plural (quebra em português), e sempre localizar a partir de uma string-fonte em inglês.

**7. Fechando o ciclo: do insight à execução**
Um relatório sozinho não move um roadmap — vira trabalho quando chega ao board de quem vai executar. Por isso o passo final foi o Action Board (transformar achados/recomendações em tarefas com prioridade sugerida) e a integração com Jira, permitindo enviar qualquer tarefa direto para o Jira Cloud com todo o contexto da sessão, sem re-digitar nada manualmente.

---

## Resultados

- Um app nativo de macOS funcional de ponta a ponta: da gravação da sessão ao relatório estruturado, sem etapas manuais de transcrição ou anotação.
- Uma abordagem de IA validada com uma referência de mercado (Maze) que prova ser possível chegar perto da profundidade de um relatório em nuvem mantendo o processamento 100% local — sem custo de API e sem dados de participantes saindo do dispositivo.
- Um modelo de relatório desenhado para gerar confiança: achados sempre hedgeados como hipótese e sempre ancorados em evidência verificável, não apenas em afirmações da IA.
- Um ciclo completo de pesquisa-para-execução: o insight de um teste de usabilidade vira, com um clique, uma tarefa rastreável no Jira — sem fricção de copiar e colar entre ferramentas.
- Um produto verdadeiramente bilíngue, incluindo a camada de IA, que se adapta automaticamente ao idioma do sistema do usuário.

---

## Stack técnica

SwiftUI · SwiftData · Apple Foundation Models (on-device) · Speech framework (transcrição on-device) · AVFoundation (captura de áudio) · Jira Cloud REST API v3
