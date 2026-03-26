# Avaliação — Engenharia de Software
**Sistema Integrado de Gestão de Farmácia — MVP Definido pelo Estudante**

Aluno: **Leonardo Delfino Vieira** 
RA: **25000296**  
Data: **25/03/2026**

---

# 1. Definição do MVP
Descreva aqui **qual parte do sistema** foi incluída no seu MVP.  

- O que está **dentro** do MVP  
   - Gestão de Estoque e Produtos
   - Compra e Fornecedorers
- O que está **fora** do MVP  
   - Operação da Farmácia e Atendimento ao Cliente
   - Contas a Pagar e Contas a Receber
   - Relatórios e Indicadores
   - Perfis de Usuários e Permissões
   - Integração de Processos
   - Objetivo do Sistema

- Por que você fez essas escolhas  
Escolhi esses MVPs pois foram os que mais me agradaram e os que eu mais tenho conhecimento devido ao meu trabalho atual, onde constantemente estou em contato com estoque de produtos, vejo diariamente produtos entrando e saindo dele e sendo entregue a clientes.

---

# 2. Regras de Negócio <br>

**RN01 —** Produtos sem estoque não podem ser comercializados.
O sistema deve bloquear automaticamente a venda de qualquer item com quantidade disponível igual a zero. <br>
**RN02 —** Cada farmácia possui estoque próprio e independente. 
Movimentações em uma unidade não afetam diretamente o estoque de outra, exceto por transferências explícitas. <br>
**RN03 —** Toda compra realizada junto a fornecedores deve gerar automaticamente um lançamento em contas a pagar, com data de vencimento e status inicial "Aberta". <br>
**RN04 —** O estoque deve ser atualizado automaticamente após qualquer movimentação: venda, devolução, perda, transferência ou reposição. Atualizações manuais não são permitidas para esses eventos. <br> 
**RN05 —**  O sistema deve emitir alertas quando a quantidade de um produto atingir ou ficar abaixo do nível mínimo cadastrado, sinalizando a necessidade de reposição. <br>

---

# 3. Requisitos Funcionais <br>

**RF01 —** O sistema deve atualizar o estoque da unidade automaticamente ao registrar uma venda, deduzindo a quantidade vendida do produto correspondente. <br>
**RF02 —** O sistema deve registrar devoluções e estornar a quantidade ao estoque da unidade que realizou a venda original. <br>
**RF03 —** O sistema deve permitir o registro de perdas de produtos, subtraindo a quantidade do estoque e armazenando o motivo da perda. <br>
**RF04 —** O sistema deve permitir transferências de produtos entre unidades, atualizando o estoque de origem (saída) e de destino (entrada) de forma simultânea. <br>
**RF05 —** O sistema deve registrar entradas de estoque vinculadas a pedidos de compra, atualizando automaticamente a quantidade disponível após confirmação do recebimento. <br>
**RF06 —** O sistema deve registrar pedidos de compra contendo: produto, quantidade, data, valor total, fornecedor e unidade receptora. <br>
**RF07 —** Ao confirmar uma compra, o sistema deve gerar automaticamente um registro em contas a pagar com valor, data de vencimento e status "Aberta", além de suportar as transições para "Paga" e "Atrasada". <br>
**RF08 —** Gerentes devem poder cadastrar novos produtos e editar informações de produtos existentes, como nome, descrição, nível mínimo de estoque e dados do fornecedor. <br>

---

# 🛡 4. Requisitos Não Funcionais <br>


**RNF01 —** Desempenho: as atualizações de estoque decorrentes de movimentações devem ser processadas em tempo real, com latência máxima de 2 segundos após o registro do evento. <br>
**RNF02 —** Confiabilidade: o sistema deve garantir consistência dos dados de estoque mesmo em cenários de falha parcial, utilizando transações atômicas para operações que envolvam múltiplas tabelas (ex.: compra + contas a pagar). <br>
**RNF03 —** Segurança e controle de acesso: apenas usuários com perfil de gerente devem ter permissão para cadastrar ou editar produtos. Operações de venda e movimentação devem estar restritas a operadores autorizados de cada unidade. <br>
**RNF04 —** Auditabilidade: todas as movimentações de estoque e registros financeiros devem manter histórico imutável com data, hora, usuário responsável e tipo de operação, possibilitando rastreabilidade completa. <br>

---

# 5. Casos de Uso 

```puml
@startumlcasos_de_uso_saude_vida

skinparam backgroundColor #FFFFFF
skinparam usecase {
  BackgroundColor #E1F5EE
  BorderColor #0F6E56
  FontColor #085041
  FontSize 13
}

skinparam actor {
  BackgroundColor #EEEDFE
  BorderColor #534AB7
  FontColor #3C3489
  FontSize 12
}

skinparam ArrowColor #444441
skinparam ArrowFontSize 11
skinparam ArrowFontColor #5F5E5A
skinparam rectangleBorderColor #B4B2A9
skinparam rectangleFontColor #444441
skinparam rectangleFontSize 13
skinparam linetype ortho

left to right direction

actor "Atendente"          as ATD
actor "Farmacêutico"       as FAR
actor "Gerente"            as GER
actor "Setor Adm."         as ADM
actor "Sistema"            as SIS

rectangle "Sistema Saúde & Vida" {

  usecase "UC01\nRegistrar venda"                as UC01
  usecase "UC02\nVerificar disponibilidade\nem estoque" as UC02
  usecase "UC03\nRegistrar devolução"            as UC03
  usecase "UC04\nRegistrar compra\nde fornecedor" as UC04
  usecase "UC05\nAtualizar estoque\napós movimentação" as UC05
  usecase "UC06\nConsultar estoque\nda unidade"  as UC06
  usecase "UC07\nCadastrar / editar\nproduto"    as UC07
  usecase "UC08\nGerenciar\ncontas a pagar"      as UC08
  usecase "UC09\nEmitir alerta de\nestoque mínimo" as UC09
  usecase "UC10\nVisualizar indicadores\nde desempenho" as UC10

}

ATD --> UC01
ATD --> UC02
ATD --> UC03

FAR --> UC06

GER --> UC04
GER --> UC06
GER --> UC07
GER --> UC10

ADM --> UC08
ADM --> UC10

SIS --> UC09

UC01 ..> UC02 : «include»
UC03 ..> UC02 : «include»
UC04 ..> UC05 : «include»

UC01 ..> UC05 : «extend»
UC03 ..> UC05 : «extend»
UC08 ..> UC05 : «extend»
UC05 ..> UC09 : «extend»

@enduml
```

**UC01** – Registrar venda (Atendente) — inclui UC02 e pode estender para UC10 se houver alerta de estoque. <br>
**UC02** – Verificar disponibilidade em estoque (include de UC01 e UC06). <br>
**UC03** – Registrar devolução (Atendente) — estende UC01 quando o cliente devolve um produto. <br>
**UC04** – Registrar compra de fornecedor (Gerente) — inclui UC05. <br>
**UC05** – Atualizar estoque após movimentação (include de UC01, UC03, UC04). <br>
**UC06** – Consultar estoque da unidade (Gerente / Farmacêutico). <br>
**UC07** – Cadastrar/editar produto (Gerente). <br>
**UC08** – Gerenciar contas a pagar (Setor Administrativo) — inclui UC04 como gatilho, estende para registrar pagamento. <br>
**UC09** – Emitir alertas de estoque mínimo (Sistema) — estende UC05 quando quantidade cai abaixo do limite. <br>
**UC10** – Visualizar indicadores de desempenho (Gerente / Setor Administrativo). <br>

**Relações include:** <br>

**UC01 inclui UC02:** toda venda obrigatoriamente verifica o estoque disponível antes de ser concluída. <br>
**UC03 inclui UC02:** toda devolução também verifica o estoque antes de atualizar a quantidade. <br>
**UC04 inclui UC05:** ao registrar uma compra, o estoque é automaticamente atualizado. <br>

**Relações extend:** <br>

**UC01 estende UC05:** a venda aciona a atualização de estoque (decremento) ao ser finalizada. <br>
**UC03 estende UC05:** a devolução aciona a atualização de estoque (incremento) ao ser registrada. <br>
**UC08 estende UC05:** quando a atualização de estoque ocorre por uma compra, ela dispara o lançamento em contas a pagar. <br>
**UC05 estende UC09:** quando a quantidade atinge o nível mínimo após qualquer movimentação, o alerta é emitido. <br>

**Atores e seus casos de uso:** <br>

**Atendente:** UC01, UC02, UC03 <br>
**Farmacêutico:** UC06 <br>
**Gerente:** UC04, UC07, UC09, UC10 <br>
**Setor Administrativo:** UC08, UC10 <br>
**Sistema (ator secundário):** UC09 (disparo automático) <br>

---

# 6. Documentação dos Casos de Uso

**UC01 — Registrar Venda** <br>
*Ator:* Atendente <br>
*Descrição:* Permite ao atendente registrar uma venda de produtos a um cliente, verificando disponibilidade em estoque e finalizando a transação com atualização automática do estoque. <br>
*Pré-condições:* <br>
•	Atendente autenticado no sistema. <br>
•	Produto(s) deve(m) estar cadastrado(s) e com estoque disponível. <br>
*Pós-condições:* <br>
•	Venda registrada com data, itens, quantidades e valor total. <br>
•	Estoque da unidade atualizado (decrementado). <br>
•	Registro financeiro gerado em contas a receber (quando aplicável). <br>
*Fluxo Principal* <br>
1.	Atendente inicia um novo atendimento no sistema. <br>
2.	Sistema solicita identificação do cliente (opcional) e dos produtos. <br>
3.	Atendente informa o(s) produto(s) e a(s) quantidade(s) desejada(s). <br>
4.	Sistema executa UC02 — Verificar disponibilidade em estoque para cada item. <br>
5.	Sistema exibe o resumo da venda com valor total. <br>
6.	Atendente confirma a venda e registra a forma de pagamento. <br>
7.	Sistema finaliza a venda, executa UC05 — Atualizar estoque e emite comprovante. <br>
*Fluxos Alternativos / Exceções* <br>
•	FA01 — Produto sem estoque: se UC02 identificar quantidade zero, o sistema bloqueia o item e exibe mensagem de indisponibilidade. O atendente pode remover o item ou cancelar a venda. <br>
•	FA02 — Cancelamento durante registro: o atendente pode cancelar a venda antes da confirmação; o sistema descarta o registro sem alterar o estoque. <br>
•	FA03 — Produto não encontrado: sistema exibe mensagem de erro e permite nova busca. <br>
*Relacionamentos* <br>
*Include:*  <br>
•	UC02 — Verificar disponibilidade em estoque <br>
•	UC05 — Atualizar estoque após movimentação (via extend) <br>
*Extend:* Nenhum. <br>

```puml

@startuml UC01_Registrar_Venda
title UC01 — Registrar Venda

start
:Informar produto(s) e quantidade(s);
:Verificar disponibilidade (UC02);
if (Produto disponível?) then (Sim)
  :Exibir resumo e valor total;
  :Confirmar pagamento;
  :Registrar venda;
  :Atualizar estoque (UC05);
  :Emitir comprovante;
else (Não)
  if (Remover item?) then (Sim)
    :Remover item e recalcular;
    :Confirmar pagamento;
    :Registrar venda;
    :Atualizar estoque (UC05);
    :Emitir comprovante;
  else (Não)
    :Cancelar venda;
  endif
endif
stop
@enduml
```

-------------------------------------

**UC02 — Verificar Disponibilidade em Estoque** <br>
*Atores:* Atendente, Farmacêutico (indireto via UC01 e UC03) <br>
*Descrição:* Consulta a quantidade disponível de um produto no estoque da unidade antes de permitir a conclusão de uma venda ou devolução. <br>
*Pré-condições:* <br>
•	Produto informado deve estar cadastrado no sistema. <br>
•	Estoque da unidade deve estar acessível. <br>
*Pós-condições:* <br>
•	Disponibilidade do produto confirmada ou negada. <br>
•	Em caso de quantidade abaixo do mínimo, alerta pode ser disparado via UC09. <br>
*Fluxo Principal* <br>
8.	Sistema recebe o código ou nome do produto a ser verificado. <br>
9.	Sistema consulta o estoque da unidade para o produto informado. <br>
10.	Sistema retorna a quantidade disponível. <br>
11.	Se a quantidade for zero, sistema sinaliza produto indisponível. <br>
12.	Se a quantidade estiver abaixo do nível mínimo, sistema aciona UC09 — Emitir alerta de estoque mínimo. <br>
13.	Resultado é retornado ao caso de uso chamador (UC01 ou UC03). <br>
*Fluxos Alternativos / Exceções* <br>
•	FA01 — Produto não cadastrado: sistema retorna erro e interrompe a operação chamadora. <br>
•	FA02 — Falha de conexão: sistema exibe mensagem de indisponibilidade temporária e impede a venda. <br>
*Relacionamentos* <br>
*Include:* Nenhum. <br>
*Extend:* <br> 
•	UC09 — Emitir alerta de estoque mínimo (quando quantidade abaixo do mínimo) <br>

```puml
@startuml UC02_Verificar_Disponibilidade
title UC02 — Verificar Disponibilidade em Estoque

start
:Receber código ou nome do produto;
if (Produto cadastrado?) then (Não)
  :Retornar erro ao chamador;
  stop
else (Sim)
  :Consultar saldo no estoque;
  if (Quantidade > 0?) then (Sim)
    if (Saldo <= nível mínimo?) then (Sim)
      :Acionar UC09 — Alerta de estoque mínimo;
    else (Não)
    endif
    :Retornar: disponível;
  else (Não)
    :Retornar: indisponível;
  endif
endif
stop
@enduml
```

-------------------------------------

**UC03 — Registrar Devolução** <br>
*Ator:* Atendente <br>
*Descrição:* Permite ao atendente registrar a devolução de produtos por parte de um cliente, revertendo o estoque e gerando os registros financeiros correspondentes. <br>
*Pré-condições:* <br>
•	Atendente autenticado no sistema. <br>
•	Venda original deve estar registrada no sistema. <br>
•	Produto a devolver deve ser identificável. <br>
*Pós-condições:* <br>
•	Devolução registrada com data, itens e motivo. <br>
•	Estoque da unidade atualizado (incrementado). <br>
•	Crédito ou reembolso gerado no financeiro, conforme política da rede. <br>
*Fluxo Principal* <br>
14.	Atendente acessa a funcionalidade de devolução. <br>
15.	Atendente informa o número da venda original ou identifica o produto a devolver. <br>
16.	Sistema localiza a venda e exibe os itens elegíveis para devolução. <br>
17.	Sistema executa UC02 — Verificar disponibilidade em estoque para confirmar que o item pode ser reintegrado. <br>
18.	Atendente seleciona o(s) item(ns) a devolver e informa o motivo. <br>
19.	Sistema confirma a devolução e executa UC05 — Atualizar estoque (incremento). <br>
20.	Sistema gera o registro financeiro de crédito ou reembolso. <br>
*Fluxos Alternativos / Exceções* <br>
•	FA01 — Venda não encontrada: sistema exibe mensagem de erro; atendente pode realizar nova busca ou cancelar. <br>
•	FA02 — Prazo de devolução expirado: sistema alerta que a devolução está fora do prazo e solicita autorização do gerente. <br>
•	FA03 — Produto com dano: atendente registra observação de dano; sistema encaminha para análise antes de reintegrar ao estoque. <br>
*Relacionamentos* <br>
*Include:*  <br>
•	UC02 — Verificar disponibilidade em estoque <br>
*Extend:*  <br>
•	UC05 — Atualizar estoque após movimentação <br>

```puml
@startuml UC03_Registrar_Devolucao
title UC03 — Registrar Devolução

start
:Informar número da venda ou produto;
if (Venda encontrada?) then (Não)
  :Exibir erro e encerrar;
  stop
else (Sim)
  if (Prazo de devolução válido?) then (Não)
    if (Gerente autorizou?) then (Não)
      :Cancelar devolução;
      stop
    else (Sim)
    endif
  else (Sim)
  endif
  :Verificar estoque (UC02);
  :Selecionar item(ns) e informar motivo;
  if (Produto com dano?) then (Sim)
    :Registrar dano e encaminhar para análise;
  else (Não)
    :Atualizar estoque — incremento (UC05);
    :Gerar crédito ou reembolso;
  endif
endif
stop
@enduml
```

-------------------------------------

**UC04 — Registrar Compra de Fornecedor** <br>
*Ator:* Gerente <br>
*Descrição:* Permite ao gerente registrar um pedido de compra junto a um fornecedor, informando os produtos, quantidades, valores e datas, gerando automaticamente a entrada no estoque e o lançamento financeiro. <br>
*Pré-condições:* <br>
•	Gerente autenticado no sistema. <br>
•	Fornecedor deve estar cadastrado. <br>
•	Produto(s) deve(m) estar cadastrado(s) no sistema. <br>
*Pós-condições:* <br>
•	Compra registrada com todos os dados obrigatórios. <br>
•	Estoque da unidade atualizado via UC05. <br>
•	Lançamento em contas a pagar gerado com status 'Aberta'. <br>
*Fluxo Principal* <br>
21.	Gerente acessa o módulo de compras e inicia um novo pedido. <br>
22.	Sistema solicita os dados da compra: fornecedor, produto, quantidade, data e valor total. <br>
23.	Gerente preenche todos os campos obrigatórios e confirma. <br>
24.	Sistema valida os dados e registra a compra. <br>
25.	Sistema executa UC05 — Atualizar estoque (incremento conforme quantidade comprada). <br>
26.	Sistema gera automaticamente lançamento em UC08 — Gerenciar contas a pagar com status 'Aberta' e data de vencimento. <br>
27.	Sistema confirma o registro e exibe resumo da operação. <br>
*Fluxos Alternativos / Exceções* <br>
•	FA01 — Fornecedor não cadastrado: sistema bloqueia o registro e orienta o gerente a cadastrá-lo antes de continuar. <br>
•	FA02 — Dados incompletos: sistema destaca os campos obrigatórios não preenchidos e impede o salvamento. <br>
•	FA03 — Produto não cadastrado: sistema orienta o gerente a executar <br> UC07 antes de prosseguir. <br>
*Relacionamentos* <br>
*Include:* <br> 
•	UC05 — Atualizar estoque após movimentação <br>
*Extend:* <br> 
•	UC08 — Gerenciar contas a pagar (lançamento automático após registro da compra) <br>

```puml
@startuml UC04_Registrar_Compra
title UC04 — Registrar Compra de Fornecedor

start
:Preencher dados da compra\n(fornecedor, produto, quantidade, valor, data);
if (Fornecedor cadastrado?) then (Não)
  :Orientar cadastro e encerrar;
  stop
else (Sim)
  if (Produto cadastrado?) then (Não)
    :Orientar UC07 e encerrar;
    stop
  else (Sim)
    if (Dados válidos?) then (Não)
      :Destacar erros;
      stop
    else (Sim)
      :Registrar compra;
      fork
        :Atualizar estoque — incremento (UC05);
      fork again
        :Gerar conta a pagar — status "Aberta";
      end fork
      :Exibir resumo da operação;
    endif
  endif
endif
stop
@enduml
```

-------------------------------------

**UC05 — Atualizar Estoque Após Movimentação** <br>
*Ator:* Sistema (acionado automaticamente por UC01, UC03 e UC04) <br>
*Descrição:* Responsável por atualizar automaticamente o saldo de estoque da unidade após qualquer movimentação registrada: venda, devolução, compra, perda ou transferência. <br>
*Pré-condições:* <br>
•	Evento de movimentação devidamente registrado (venda, devolução ou compra confirmada). <br>
•	Produto e unidade identificados. <br>
*Pós-condições:* <br>
•	Saldo do produto atualizado no estoque da unidade. <br>
•	Histórico de movimentação registrado com data, tipo e responsável. <br>
•	UC09 acionado se saldo atingir o nível mínimo. <br>
*Fluxo Principal* <br>
28.	Sistema recebe o evento de movimentação (tipo, produto, quantidade e unidade). <br>
29.	Sistema identifica se a movimentação é de entrada (compra, devolução) ou saída (venda, perda, transferência). <br>
30.	Sistema calcula o novo saldo: saldo atual ± quantidade movimentada. <br>
31.	Sistema persiste o novo saldo no estoque da unidade. <br>
32.	Sistema registra o histórico da movimentação. <br>
33.	Sistema verifica se o novo saldo está abaixo do nível mínimo; se sim, aciona UC09. <br>
*Fluxos Alternativos / Exceções* <br>
•	FA01 — Saldo resultante negativo: sistema bloqueia a operação e retorna erro ao caso de uso chamador (impede venda sem estoque). <br>
•	FA02 — Falha de persistência: sistema registra o erro e notifica o administrador; operação é desfeita para manter consistência. <br>
*Relacionamentos* <br>
*Include:* Nenhum. <br>
*Extend:* <br>
•	UC09 — Emitir alerta de estoque mínimo (quando saldo atingir ou ficar abaixo do mínimo) <br>

```puml
@startuml UC05_Atualizar_Estoque
title UC05 — Atualizar Estoque Após Movimentação

start
:Receber evento de movimentação\n(tipo, produto, quantidade);
if (Tipo de movimentação?) then (Entrada)
  :Saldo = saldo atual + quantidade;
else (Saída)
  :Saldo = saldo atual - quantidade;
endif
if (Novo saldo >= 0?) then (Não)
  :Bloquear operação e retornar erro;
  stop
else (Sim)
  :Persistir novo saldo;
  :Registrar histórico da movimentação;
  if (Saldo <= nível mínimo?) then (Sim)
    :Acionar UC09 — Alerta de estoque mínimo;
  else (Não)
  endif
  :Retornar confirmação ao chamador;
endif
stop
@enduml
```

-------------------------------------

**UC06 — Consultar Estoque da Unidade** <br>
*Atores:* Gerente, Farmacêutico <br>
*Descrição:* Permite a gerentes e farmacêuticos consultar o saldo atual de produtos no estoque da unidade, com filtros por produto, categoria ou situação (normal, abaixo do mínimo, zerado). <br>
*Pré-condições:* <br>
•	Usuário autenticado com perfil de Gerente ou Farmacêutico. <br>
•	Estoque da unidade atualizado e acessível. <br>
*Pós-condições:* <br>
•	Lista de produtos exibida com saldos atuais. <br>
•	Produtos abaixo do nível mínimo destacados visualmente. <br>
*Fluxo Principal* <br>
34.	Usuário acessa o módulo de estoque. <br>
35.	Sistema exibe o estoque completo da unidade com saldo atual de cada produto. <br>
36.	Usuário pode filtrar por nome, categoria, ou situação do estoque. <br>
37.	Sistema aplica os filtros e exibe os resultados. <br>
38.	Usuário visualiza o saldo, nível mínimo e status de cada produto.
39.	Usuário pode exportar o relatório ou navegar para detalhes de um produto. <br>
*Fluxos Alternativos / Exceções* <br>
•	FA01 — Nenhum produto encontrado com os filtros aplicados: sistema exibe mensagem informativa e sugere ampliar os critérios de busca. <br>
•	FA02 — Estoque vazio: sistema informa que não há produtos cadastrados e orienta para UC07. <br>
*Relacionamentos* <br>
*Include:* Nenhum. <br>
*Extend:* Nenhum. <br>

```puml
@startuml UC06_Consultar_Estoque
title UC06 — Consultar Estoque da Unidade

start
:Acessar módulo de estoque;
:Exibir estoque completo da unidade;
if (Deseja aplicar filtros?) then (Sim)
  :Informar filtros (nome, categoria, situação);
  :Aplicar filtros;
else (Não)
endif
if (Produtos encontrados?) then (Não)
  :Exibir mensagem — sem resultados;
else (Sim)
  :Destacar produtos abaixo do nível mínimo;
  :Exibir saldo e status de cada produto;
  if (Deseja exportar relatório?) then (Sim)
    :Gerar e disponibilizar relatório;
  else (Não)
  endif
endif
stop
@enduml
```

-------------------------------------

**UC07 — Cadastrar / Editar Produto** <br>
*Ator:* Gerente <br>
*Descrição:* Permite ao gerente cadastrar novos produtos no sistema ou atualizar informações de produtos existentes, incluindo nome, descrição, categoria, nível mínimo de estoque e fornecedor associado. <br>
*Pré-condições:* <br>
•	Gerente autenticado no sistema. <br>
•	Para edição: produto deve estar previamente cadastrado. <br>
*Pós-condições:* <br>
•	Produto criado ou atualizado com todas as informações fornecidas. <br>
•	Nível mínimo de estoque definido, habilitando os alertas automáticos do UC09. <br>
*Fluxo Principal* <br>
40.	Gerente acessa o cadastro de produtos. <br>
41.	Para novo produto: gerente seleciona 'Novo produto' e preenche os campos obrigatórios (nome, categoria, nível mínimo, fornecedor). <br>
42.	Para edição: gerente busca o produto existente e seleciona 'Editar'. <br>
43.	Gerente atualiza os campos desejados. <br>
44.	Sistema valida os dados informados. <br>
45.	Gerente confirma o cadastro ou a edição. <br>
46.	Sistema salva as informações e confirma a operação. <br>
*Fluxos Alternativos / Exceções* <br>
•	FA01 — Produto duplicado: sistema identifica nome ou código já cadastrado e alerta o gerente antes de salvar. <br>
•	FA02 — Campos obrigatórios vazios: sistema destaca os campos e impede o salvamento. <br>
•	FA03 — Nível mínimo inválido (valor negativo ou não numérico): sistema exibe erro de validação. <br>
*Relacionamentos* <br>
*Include:* Nenhum. <br>
*Extend:* Nenhum. <br>

```puml
@startuml UC07_Cadastrar_Produto
title UC07 — Cadastrar / Editar Produto

start
if (Operação?) then (Novo produto)
  :Preencher dados\n(nome, categoria, nível mínimo, fornecedor);
else (Editar existente)
  :Buscar e carregar produto;
  :Alterar campos desejados;
endif
if (Dados válidos e únicos?) then (Não)
  :Exibir erros de validação;
  stop
else (Sim)
  :Salvar produto;
  :Confirmar operação;
endif
stop
@enduml
```

-------------------------------------

**UC08 — Gerenciar Contas a Pagar** <br>
*Ator:* Setor Administrativo <br>
*Descrição:* Permite ao setor administrativo visualizar, registrar, atualizar e quitar lançamentos em contas a pagar, incluindo os gerados automaticamente por compras de fornecedores. <br>
*Pré-condições:* <br>
•	Usuário autenticado com perfil administrativo.
•	Para quitação: lançamento deve existir com status 'Aberta' ou 'Atrasada'. <br>
*Pós-condições:* <br>
•	Lançamentos criados, atualizados ou quitados corretamente. <br>
•	Status atualizado: Aberta, Paga ou Atrasada. <br>
•	Histórico financeiro mantido com rastreabilidade completa. <br>
*Fluxo Principal* <br>
47.	Usuário acessa o módulo financeiro de contas a pagar.
48.	Sistema exibe a lista de lançamentos com status, valor, fornecedor e data de vencimento. <br>
49.	Usuário pode filtrar por status, fornecedor, período ou unidade. <br>
50.	Para quitar: usuário seleciona o lançamento e registra o pagamento com data e forma. <br>
51.	Sistema atualiza o status para 'Paga' e registra o histórico. <br>
52.	Sistema verifica lançamentos vencidos e atualiza automaticamente para 'Atrasada'. <br>
53.	Usuário pode exportar relatório de contas a pagar. <br>
*Fluxos Alternativos / Exceções* <br>
•	FA01 — Lançamento já quitado: sistema bloqueia nova quitação e exibe mensagem informativa. <br>
•	FA02 — Data de pagamento inválida (futura): sistema exibe alerta e solicita confirmação do usuário. <br>
•	FA03 — Lançamento não encontrado: sistema exibe mensagem e sugere ampliar os filtros de busca. <br>
*Relacionamentos* <br>
*Include:* Nenhum. <br>
*Extend:* <br> 
•	UC05 — gerado indiretamente por UC04 que dispara tanto UC05 quanto UC08 <br>

```puml
@startuml UC08_Contas_Pagar
title UC08 — Gerenciar Contas a Pagar

start
:Acessar módulo de contas a pagar;
:Exibir lançamentos;
:Atualizar lançamentos vencidos para "Atrasada";
if (Deseja filtrar?) then (Sim)
  :Aplicar filtros;
else (Não)
endif
if (Ação desejada?) then (Quitar)
  if (Lançamento já quitado?) then (Sim)
    :Exibir aviso e encerrar;
    stop
  else (Não)
    :Registrar data e forma de pagamento;
    :Atualizar status para "Paga";
    :Registrar histórico;
  endif
else (Exportar)
  :Gerar e disponibilizar relatório;
endif
stop
@enduml
```

-------------------------------------

**UC09 — Emitir Alerta de Estoque Mínimo* *<br>
*Ator:* Sistema (ator secundário); notificação recebida por Gerente e Farmacêutico <br>
*Descrição:* O sistema monitora automaticamente os saldos de estoque e emite alertas sempre que um produto atingir ou ficar abaixo do nível mínimo cadastrado, orientando a necessidade de reposição. <br>
*Pré-condições:* <br>
•	Nível mínimo configurado para o produto (via UC07). <br>
•	UC05 executa o com novo saldo calculado. <br>
*Pós-condições:* <br>
•	Alerta registrado no sistema com produto, unidade, saldo atual e nível mínimo. <br>
•	Notificação enviada ao gerente responsável pela unidade.
•	Produto destacado na consulta de estoque (UC06) <br>. <br>
*Fluxo Principal* <br>
54.	UC05 finaliza a atualização do saldo de um produto. <br>
55.	Sistema compara o novo saldo com o nível mínimo cadastrado. <br>
56.	Se saldo <= nível mínimo, sistema gera um registro de alerta. <br>
57.	Sistema notifica o gerente da unidade sobre a necessidade de reposição. <br>
58.	Alerta é exibido no painel de indicadores e na tela de consulta de estoque. <br>
59.	Gerente pode, a partir do alerta, iniciar UC04 — Registrar compra de fornecedor. <br>
*Fluxos Alternativos / Exceções* <br>
•	FA01 — Nível mínimo não configurado: sistema ignora a verificação e registra log de configuração pendente.
•	FA02 — Alerta já existente e não resolvido: sistema atualiza o alerta existente sem duplicá-lo. <br>
*Relacionamentos* <br>
*Include:* Nenhum. <br>
*Extend:* Nenhum. <br>

```puml
@startuml UC09_Alerta_Estoque
title UC09 — Emitir Alerta de Estoque Mínimo

start
:Receber novo saldo do UC05;
if (Nível mínimo configurado?) then (Não)
  :Registrar log de configuração pendente;
  stop
else (Sim)
  if (Saldo <= nível mínimo?) then (Não)
    stop
  else (Sim)
    if (Alerta já existe?) then (Sim)
      :Atualizar alerta existente;
    else (Não)
      :Gerar novo alerta;
    endif
    :Notificar gerente da unidade;
    if (Gerente deseja repor?) then (Sim)
      :Acionar UC04 — Registrar compra;
    else (Não)
    endif
  endif
endif
stop
@enduml
```

-------------------------------------

**UC10 — Visualizar Indicadores de Desempenho** <br>
*Atores:* Gerente, Setor Administrativo <br> 
*Descrição:* Disponibiliza painéis e relatórios com indicadores <br> operacionais e financeiros da rede, como volume de vendas, produtos mais vendidos, posição de estoque e status financeiro por unidade. <br>
*Pré-condições:* <br>
•	Usuário autenticado com perfil de Gerente ou Setor Administrativo. <br>
•	Dados de vendas, estoque e financeiro disponíveis no sistema. <br> <br>
*Pós-condições:* <br>
•	Indicadores exibidos conforme período e unidade selecionados. <br>
•	Relatórios disponíveis para exportação. <br>
*Fluxo Principal* <br>
60.	Usuário acessa o módulo de indicadores.
61.	Sistema exibe o painel padrão com indicadores do período atual. <br>
62.	Usuário seleciona o período de análise e a(s) unidade(s) desejada(s). <br>
63.	Sistema processa e exibe os indicadores: total de vendas, ticket médio, produtos críticos em estoque, contas em atraso, entre outros. <br>
64.	Usuário pode detalhar um indicador específico para análise mais  <br>aprofundada. <br>
65.	Usuário pode exportar os dados em formato de relatório. <br>
*Fluxos Alternativos / Exceções* <br>
•	FA01 — Período sem dados: sistema informa que não há registros no intervalo selecionado. <br>
•	FA02 — Falha no processamento: sistema exibe mensagem de erro e registra log; usuário pode tentar novamente. <br>
*Relacionamentos* <br>
*Include:* Nenhum. <br>
*Extend:* Nenhum. <br>

```puml
@startuml UC10_Indicadores
title UC10 — Visualizar Indicadores de Desempenho

start
:Acessar módulo de indicadores;
:Selecionar período e unidade(s);
if (Dados disponíveis?) then (Não)
  :Informar ausência de registros;
  stop
else (Sim)
  :Exibir indicadores\n(vendas, ticket médio, estoque crítico, atrasos);
  if (Detalhar indicador?) then (Sim)
    :Exibir análise aprofundada;
  else (Não)
  endif
  if (Exportar relatório?) then (Sim)
    :Gerar e disponibilizar relatório;
  else (Não)
  endif
endif
stop
@enduml
```

---


