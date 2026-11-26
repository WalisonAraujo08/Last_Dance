# Raio-X do Prato na Balança: Inteligência de Mix e Margem (CMV) em Tempo Real

FMU TECH | HACKATHON TECH — DESAFIO GRUPO BOVINUS
Solução de Visão Computacional Não Invasiva para a Unidade Bovinus Grill & Beer (Rua Augusta — SP)

---

## Integrantes do Grupo e Links da Entrega

- Integrante 1 (Líder): Walison Araujo Santana — RA: 1696860
- Integrante 2: Cauã Tomas da Paixão — RA: 1696689
- Repositório Público (GitHub): [https://github.com/WalisonAraujo08/Last_Dance]
- Imagens Arquivos: [https://github.com/WalisonAraujo08/Last_Dance/blob/main/Imagens/files.jpeg]
- Imagens Graficos: [https://github.com/WalisonAraujo08/Last_Dance/blob/main/Imagens/gr%C3%A1ficos.jpeg]
- Slide: [https://github.com/WalisonAraujo08/Last_Dance/tree/main/Slide]
- Vídeo: [https://github.com/WalisonAraujo08/Last_Dance/blob/main/V%C3%ADdeo/v%C3%ADdeos.mp4]

---

## 1. Resumo Executivo e Contexto do Problema

A unidade Bovinus Grill & Beer da Rua Augusta (São Paulo) opera no modelo de almoço por quilo de segunda a domingo (das 11h30 às 15h30), atendendo cerca de 600 clientes por dia nos dias úteis, com pico crítico concentrado entre 12h10 e 13h20. Com preço atual de R$ 119,00/kg e um buffet de alta variedade (cerca de 30 saladas, 30 pratos quentes, 10 cortes de churrasco e 20 sobremesas), o grande diferencial competitivo da casa é a churrasqueira.

Durante o briefing do Hackathon, a diretoria apontou que a dor mais crítica para o resultado financeiro da loja é a cegueira sobre o mix do prato servido: o sistema de balança integrado ao PDV (JS SoftCOM) registra com precisão o peso total em gramas e o valor cobrado na comanda, mas a gestão não possui nenhuma métrica estruturada sobre o que o cliente efetivamente colocou no prato.

O Dilema Financeiro do Quilo (CMV no Escuro):
Um prato de 600 g composto majoritariamente por folhas e acompanhamentos e um prato de 600 g composto por picanha e cortes nobres geram exatamente o mesmo faturamento na balança (R$ 71,40), porém possuem Custos de Mercadoria Vendida (CMV) radicalmente opostos. Hoje, a gerência estima esse consumo apenas pelo "feeling" das reposições de cubas da cozinha, sem visibilidade por horário, dia da semana ou perfil de prato.

Nossa solução — o Raio-X do Prato na Balança — utiliza visão computacional leve posicionada acima das 3 balanças já existentes para identificar automaticamente a proporção visual de Carnes/Churrasco, Guarnições Quentes e Saladas no instante da pesagem, cruzando esse percentual com o peso exato da balança para transformar cada prato em inteligência de margem em tempo real.

---

## 2. Arquitetura Técnica e Funcionamento da Solução

O sistema foi projetado para rodar de forma modular e desacoplada do software de caixa, evitando qualquer risco à operação diária do restaurante:

Fluxo do Sistema:
[1. Câmera Zenital 90 graus] -> [2. Recorte do Prato (OpenCV)] -> [3. Segmentação IA (CMV)] -> [4. Cruzamento com Peso da Balança e Cálculo de Margem Bruta] -> [5. Painel Gerencial]

1. Captura Zenital Passiva (Hardware): Uma câmera Full HD posicionada a ~70 cm acima do prato de pesagem de cada uma das 3 balanças captura uma imagem superior (ângulo de 90 graus) no exato milissegundo em que o peso estabiliza e o operador bipa a comanda.
2. Isolamento do Prato (Pré-processamento OpenCV): O algoritmo detecta o contorno circular do prato branco e aplica uma máscara de recorte, descartando totalmente a bancada de inox, mãos do operador ou o ambiente ao redor.
3. Segmentação por Grupos de Custo (Visão Computacional / Rede Neural): O modelo classifica os pixels internos do prato em três grandes grupos estratégicos de CMV:
   - Carnes & Churrasco (custo alto);
   - Guarnições & Pratos Quentes (custo médio);
   - Saladas & Folhas (custo baixo).
4. Motor de Cálculo de CMV e Margem: A proporção de área ocupada por cada grupo é multiplicada pelo peso líquido informado pela balança (ex.: 620 g) e pelos custos médios unitários cadastrados pela gerência, estimando o custo real daquela comanda em menos de 300 ms.
5. Painel Gerencial em Tempo Real (Dashboard): Os dados consolidados alimentam um painel web acessível pelo gerente e pelo chef executivo, exibindo o mix do turno, alertas de queda de margem por horário e ranking de saída por categoria.

---

## 3. Operação no Salão: Atrito Zero, Privacidade, Integração e Contingência

### 3.1. Zero Atrito na Fila (Velocidade para o Público Executivo)
Como o público predominante da unidade Augusta é formado por executivos e trabalhadores de escritórios e bancos da região — que dispõem de apenas 30 a 40 minutos totais para almoçar — qualquer segundo extra na fila gera desistência na escada de entrada. Por isso, o Raio-X do Prato exige zero interação humana adicional: o cliente apenas apoia o prato na balança como já faz hoje, e o operador bipa a comanda. A foto e o processamento ocorrem em segundo plano em menos de 0,3 segundo, sem exigir que o cliente aponte o prato, aperte botões ou baixe aplicativos.

### 3.2. Privacidade Total e Conformidade com a LGPD
A solução respeita integralmente a Lei Geral de Proteção de Dados (LGPD) e a política de acolhimento humanizado da casa:
- Enquadramento Exclusivo do Alimento: Por utilizar câmeras fixadas na vertical (visão zenital de cima para baixo) focadas exclusivamente na superfície da balança, nenhum rosto de cliente, operador de balança ou garçom é capturado.
- Anonimização Nativa: O registro é vinculado apenas ao número sequencial da comanda física (0001 a 9999) e ao timestamp da pesagem, sem qualquer coleta de CPF, nome ou biometria facial. Além disso, as imagens brutas podem ser descartadas automaticamente após a extração das métricas numéricas, armazenando apenas os indicadores tabulares.

### 3.3. Integração Leve com o Sistema Atual (JS SoftCOM)
Respeitando o escopo do hackathon de não substituir o sistema de pagamentos nem exigir alterações complexas no ERP atual (JS SoftCOM), a integração ocorre de forma não invasiva em dois níveis possíveis:
- Nível 1 — Escuta Passiva de Serial/Log da Balança (Plug-and-Play): As balanças comerciais enviam o peso estabilizado via porta serial/USB ou rede local para o terminal do operador. Um serviço leve em Python lê apenas o pacote de peso estabilizado (ou o log de registro da comanda no servidor local) e dispara o gatilho da câmera sem interferir na gravação original do JS SoftCOM.
- Nível 2 — Modo Independente para Piloto Imediato: Durante a fase de validação na loja, o sistema pode operar em paralelo apenas com gatilho por detecção de presença do prato na balança, gerando relatórios estatísticos por faixa horária sem sequer tocar na rede do PDV.

### 3.4. Pontos Críticos de Engenharia e Contingência Operacional
Para garantir que a solução funcione nas condições reais de um restaurante movimentado (e não apenas em laboratório), incorporamos quatro salvaguardas práticas ao projeto:
1. Tratamento de Sobreposição de Alimentos (Fator de Densidade): Em pratos por quilo, é comum o cliente colocar carne sobre o arroz. O modelo aplica fatores de densidade calibráveis (gramas por cm2) por categoria e realiza uma calibragem semanal cruzando a estimativa visual da semana com o total de cubas de carne repostas pela cozinha, corrigindo automaticamente qualquer viés de sobreposição.
2. Proteção contra Vapor e Reflexo de Iluminação: Comida quente emite vapor e pratos brancos refletem luzes do teto. O suporte da câmera prevê lente com filtro polarizador simples e distância segura de 70 cm com iluminação LED difusa dedicada, garantindo nitidez constante.
3. Contingência Total (Modo Fail-Safe): Se houver queda de internet, falha na câmera ou desligamento do computador de análise, a balança e o sistema JS SoftCOM continuam pesando e cobrando normalmente. O Raio-X nunca atua como ponto único de falha da operação.
4. Inteligência Acionável para o Buffet (Além do Relatório): Mais do que mostrar gráficos, o painel traduz os dados em ações práticas para o gerente: se o custo médio subir acima da meta às 12h30, o sistema sugere destacar guarnições de alta margem logo na entrada do buffet (antes da churrasqueira) ou ajustar o tamanho do corte servido na chapa.

---

## 4. Orçamento de Hardware e Análise de Retorno (ROI)

Como a unidade Augusta possui atualmente 3 balanças de pesagem (sem câmeras dedicadas sobre elas), estruturamos um orçamento enxuto com equipamentos comerciais de fácil reposição e baixo custo:

- Webcam Full HD 1080p com foco fixo (ex.: Intelbras WCI1080p ou Logitech): 3 unidades x R$ 240,00 = R$ 720,00
- Suporte articulado metálico de fixação superior (braço 75 cm): 3 unidades x R$ 85,00 = R$ 255,00
- Cabo extensor USB 2.0 ativo blindado (5 metros) + filtro polarizador: 3 unidades x R$ 45,00 = R$ 135,00
- Processamento Local (Mini-PC dedicado ou reaproveitamento de terminal): 1 unidade = R$ 0,00*
- INVESTIMENTO TOTAL ESTIMADO (CENÁRIO ENXUTO): R$ 1.110,00

*Nota: O modelo roda perfeitamente em CPU comum no terminal já existente próximo às balanças. Caso a diretoria opte por um Mini-PC exclusivo (Core i5 / 8GB RAM), acrescenta-se aproximadamente R$ 1.350,00 ao orçamento.

### Justificativa Financeira e Tempo de Retorno (Payback)
- Base Operacional da Loja: 600 almoços/dia x peso médio estimado de 550 g a R$ 119,00/kg = ticket médio de aproximadamente R$ 65,45, representando um faturamento diário aproximado de R$ 39.270,00 apenas no almoço.
- Impacto da Otimização de Mix: Com visibilidade real sobre o mix dos pratos, ações simples de merchandising no buffet (posicionamento estratégico de guarnições atrativas antes da churrasqueira e controle de porcionamento no corte) permitem reduzir o CMV médio em apenas 1,5% a 2,0% sem reduzir a satisfação do cliente.
- Economia Gerada e Payback: Uma economia conservadora de 1,5% sobre o faturamento diário representa R$ 589,00 por dia útil (~R$ 12.950,00/mês). Dessa forma, o investimento total de R$ 1.110,00 se paga em menos de 2 dias úteis de operação.

---

## 5. Guia de Execução do Protótipo (Google Colab e GitHub)

O código do protótipo foi desenvolvido em Python puro com bibliotecas padrão de ciência de dados e visão computacional (NumPy, Pandas, OpenCV, Matplotlib e IPyWidgets), rodando 100% gratuitamente no Google Colab sem exigir placa de vídeo (GPU) ou chaves de API.

### Passo a Passo para Rodar no Google Colab
1. Abrir o Ambiente: Acesse https://colab.new no navegador (ou abra o arquivo .ipynb diretamente pelo repositório GitHub).
2. Colar e Executar as Células: Cole os blocos de código em sequência e clique em "Ambiente de execução > Executar tudo" (Ctrl + F9).
3. Imagens de Teste Automáticas (e Como Usar Fotos Reais): Na primeira célula, o script cria automaticamente a pasta "exemplos_pratos/" contendo 6 imagens sintéticas realistas vistas de cima (prato leve de salada, prato executivo equilibrado, prato pesado em picanha, etc.). Caso queira testar com fotos reais tiradas pelo celular, basta clicar no ícone de pasta na barra lateral esquerda do Colab, abrir a pasta "exemplos_pratos/" e arrastar suas imagens (.jpg ou .png) para dentro dela.
4. Interagir com o Painel: Na última célula, utilize os seletores interativos para alternar entre os pratos, ajustar o peso lido na balança (em gramas) e visualizar o Raio-X segmentado, o cálculo de margem e os indicadores consolidados do turno.
