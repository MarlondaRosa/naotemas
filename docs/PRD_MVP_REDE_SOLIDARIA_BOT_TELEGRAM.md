# PRD - MVP Bot Telegram Rede Solidaria

## Status

Rascunho inicial para orientar o inicio do projeto.

## Fonte

Conceito inicial: GitHub issue #1, "Concept Idea".

## Problema

Doacoes comunitarias podem se perder por falta de registro, baixa visibilidade
de estoque, ausencia de historico e dificuldade de verificar se a ajuda chegou
ao beneficiario correto.

O produto deve reduzir esse risco com um bot simples de Telegram que conduza
operacoes essenciais e registre cada etapa com protocolo.

## Objetivo do MVP

Criar um bot de Telegram para uma rede solidaria que permita:

- Registrar recebimento de doacoes por centros de apoio.
- Registrar entregas para beneficiarios.
- Consultar estoque e historico operacional.
- Confirmar recebimento por protocolo.
- Auditar centros de apoio por verificadores comunitarios.

## Nao objetivos nesta fase

- Painel web administrativo.
- Pagamentos, repasses ou gestao financeira.
- Matching automatico sofisticado entre demanda e estoque.
- Geolocalizacao em tempo real.
- Cadastro completo de voluntarios fora do papel de verificador.
- Analytics avancado.

## Perfis

### Beneficiario

Pessoa que solicita ou recebe apoio.

Fluxos do MVP:

- Consultar historico.
- Confirmar recebimento por protocolo.
- Solicitar apoio de forma simples.
- Atualizar cadastro basico.

### Centro de apoio

Organizacao que recebe doacoes e realiza entregas.

Fluxos do MVP:

- Registrar recebimento.
- Registrar entrega.
- Cadastrar centro.
- Solicitar auditoria.
- Consultar estoque.
- Consultar historico.

### Verificador comunitario

Pessoa responsavel por validar centros e registrar auditorias.

Fluxos do MVP:

- Ver nova verificacao.
- Registrar auditoria agendada.
- Reportar irregularidade.
- Consultar historico.

## Entidades do MVP

### Beneficiarios

Campos iniciais:

- id
- nome_completo
- cpf
- telefone
- estado
- cidade
- rua
- quantidade_pessoas_familia
- telegram_user_id
- criado_em
- atualizado_em

Regras:

- CPF deve ser unico quando informado.
- Rua e opcional.
- Beneficiario pode ser criado durante uma entrega.

### Centros de apoio

Campos iniciais:

- id
- nome_organizacao
- cnpj
- telefone
- email
- responsavel
- cep
- estado
- cidade
- rua
- numero
- dias_funcionamento
- horario_atendimento
- status_validacao
- telegram_user_id
- criado_em
- atualizado_em

Status sugeridos:

- aguardando_validacao
- ativo
- reprovado
- suspenso

Regras:

- Centro novo entra como `aguardando_validacao`.
- Apenas centro ativo pode registrar entregas no fluxo definitivo.
- No MVP, recebimentos podem ser registrados por centro aguardando validacao,
  mas devem ficar marcados como pendentes de validacao.

### Verificadores

Campos iniciais:

- id
- nome
- telefone
- estado
- cidade
- telegram_user_id
- status
- criado_em
- atualizado_em

Status sugeridos:

- ativo
- inativo
- suspenso

### Itens de doacao

Campos iniciais:

- id
- centro_id
- categoria
- nome
- quantidade_atual
- criado_em
- atualizado_em

Categorias iniciais:

- alimentos
- medicamentos
- roupas
- brinquedos
- higiene
- moveis
- outros

Regras:

- Estoque e derivado das movimentacoes.
- `quantidade_atual` pode existir como saldo materializado para consulta rapida,
  mas deve ser atualizado apenas por recebimento ou entrega.

### Movimentacoes de estoque

Campos iniciais:

- id
- protocolo
- centro_id
- item_id
- tipo
- quantidade
- observacao
- criado_por_tipo
- criado_por_id
- criado_em

Tipos:

- recebimento
- entrega
- ajuste_auditoria

Regras:

- Toda movimentacao deve ter protocolo.
- Entrega nao pode deixar saldo negativo.
- Ajuste de auditoria so deve ser criado a partir de auditoria registrada.

### Entregas

Campos iniciais:

- id
- protocolo
- centro_id
- beneficiario_id
- item_id
- quantidade
- status_confirmacao
- confirmado_em
- criado_em

Status sugeridos:

- aguardando_confirmacao
- confirmado
- contestado

Regras:

- Cada entrega gera uma movimentacao de estoque do tipo `entrega`.
- Beneficiario confirma recebimento informando o protocolo.
- Se o beneficiario responder que nao recebeu, status vira `contestado`.

### Auditorias

Campos iniciais:

- id
- protocolo
- centro_id
- verificador_id
- motivo
- resultado
- observacao
- criado_em
- concluido_em

Motivos:

- auditoria_periodica
- denuncia
- divergencia_estoque
- outro

Resultados:

- aprovado
- aprovado_parcialmente
- reprovado

Regras:

- Toda auditoria gera protocolo.
- Auditoria pode atualizar o status de validacao do centro.
- Auditoria pode gerar ajuste de estoque somente quando houver divergencia
  documentada.

## Protocolos

Formatos iniciais:

- `RCB-AAAA-NNN` para recebimentos.
- `ENT-AAAA-NNN` para entregas.
- `AUD-AAAA-NNN` para auditorias.

Regras:

- Protocolos devem ser unicos.
- O ano deve usar a data de criacao da operacao.
- A sequencia pode ser global por tipo no MVP.

## Fluxos do Telegram

### Primeiro contato

Mensagem inicial:

```text
Ola.
Bem-vindo a Rede Solidaria.

Conectamos centros de apoio, beneficiarios e verificadores comunitarios para
garantir que as doacoes cheguem a quem precisa com transparencia.

Como deseja continuar?
1. Beneficiario
2. Centro de Apoio
3. Verificador Comunitario
```

### Centro - registrar recebimento

Passos:

1. Nome do doador.
2. Telefone do doador, opcional.
3. Estado.
4. Cidade.
5. Categoria da ajuda.
6. Nome do item.
7. Quantidade.
8. Resumo para confirmacao.
9. Criar protocolo `RCB`.
10. Atualizar estoque.

Resultado esperado:

```text
Recebimento registrado.
Protocolo: RCB-2026-001
Item: Cesta Basica
Quantidade: 20
```

### Centro - registrar entrega

Passos:

1. Informar CPF do beneficiario.
2. Se existir, exibir beneficiario encontrado.
3. Se nao existir, cadastrar beneficiario.
4. Listar itens disponiveis no centro.
5. Informar quantidade entregue.
6. Validar saldo.
7. Criar protocolo `ENT`.
8. Baixar estoque.
9. Marcar entrega como `aguardando_confirmacao`.

Resultado esperado:

```text
Entrega registrada.
Protocolo: ENT-2026-455
```

### Beneficiario - confirmar recebimento

Passos:

1. Informar protocolo.
2. Exibir item, quantidade e centro.
3. Perguntar se recebeu.
4. Marcar como `confirmado` ou `contestado`.

### Centro - consultar estoque

Exibir saldo agrupado por categoria:

```text
Estoque atual:

Alimentos
- Cesta Basica: 15
- Leite Integral: 20

Higiene
- Sabonete: 50
```

### Verificador - auditar centro

Passos:

1. Receber ou selecionar centro pendente.
2. Confirmar se centro existe.
3. Confirmar se esta funcionando.
4. Confirmar se estoque bate parcialmente, totalmente ou nao bate.
5. Registrar observacao.
6. Registrar resultado.
7. Criar protocolo `AUD`.

## Regras de negocio

- Toda operacao operacional gera protocolo.
- Estoque aumenta apenas por recebimento.
- Estoque diminui apenas por entrega ou ajuste de auditoria.
- Entrega nao pode consumir item sem saldo suficiente.
- Beneficiario pode ser cadastrado durante a entrega.
- Centro novo precisa de validacao comunitaria.
- Auditoria deve preservar historico, nao apagar operacoes antigas.
- Historico deve ser consultavel por centro, beneficiario e verificador.

## Criterios de aceite

- Um centro consegue registrar recebimento e ver o saldo aumentar.
- Um centro consegue registrar entrega e ver o saldo diminuir.
- Uma entrega gera protocolo e fica aguardando confirmacao.
- Um beneficiario consegue confirmar recebimento pelo protocolo.
- Uma negativa de recebimento marca a entrega como contestada.
- Um verificador consegue registrar auditoria de um centro.
- O estoque atual pode ser consultado por categoria.
- O historico pode ser consultado por protocolo.

## Ordem de implementacao recomendada

1. Scaffold tecnico do projeto.
2. Banco de dados e migrations.
3. Gerador de protocolos.
4. Cadastro e sessao de usuarios do Telegram.
5. Fluxo de registrar recebimento.
6. Consulta de estoque.
7. Cadastro de beneficiario durante entrega.
8. Fluxo de registrar entrega.
9. Confirmacao de recebimento.
10. Fluxo de auditoria.
11. Historico por protocolo.

## Decisoes pendentes

- Banco inicial: PostgreSQL, SQLite ou Supabase.
Resposta: Supabase.
- Hospedagem do bot.
Local, por hora, posteriormente em uma VPS.
- Estrategia de sessao conversacional no Telegram.
Resposta: Me ajude a decidir a melhor estratégia
- Se centro aguardando validacao pode ou nao registrar entregas no MVP.
Resposta: Pode.
- Se documentos de centro serao armazenados no MVP ou apenas referenciados.
Resposta: Armazenados