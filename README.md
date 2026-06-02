# NaoTemas - Rede Solidaria

NaoTemas e um projeto para organizar uma rede solidaria operada por Telegram,
conectando centros de apoio, beneficiarios e verificadores comunitarios.

O objetivo do MVP e garantir rastreabilidade simples: toda doacao recebida,
entrega realizada e auditoria de centro deve gerar um protocolo e ficar
consultavel depois.

## Conceito

Em vez de tratar o bot como uma lista de menus, o produto deve nascer a partir
dos fluxos de negocio:

- Centros de apoio registram doacoes recebidas, entregas e consultam estoque.
- Beneficiarios solicitam apoio e confirmam recebimentos por protocolo.
- Verificadores comunitarios validam centros e registram auditorias.
- A rede acompanha estoque, historico e divergencias com transparencia.

## MVP

O MVP deve ter apenas quatro entidades principais:

- Beneficiario
- Centro de apoio
- Verificador comunitario
- Item de doacao

E tres operacoes centrais:

- Receber doacao
- Entregar doacao
- Auditar centro

Consultar estoque, consultar historico e confirmar recebimento entram desde o
inicio porque sustentam a confianca operacional, mas devem nascer como
consequencia dessas tres operacoes.

## Por onde comecar

1. Fechar o PRD do MVP em `docs/PRD_MVP_REDE_SOLIDARIA_BOT_TELEGRAM.md`.
2. Definir o modelo de dados e os formatos de protocolo.
3. Criar o scaffold tecnico do bot Telegram com TypeScript.
4. Implementar o fluxo de recebimento de doacao.
5. Implementar o fluxo de entrega com consumo de estoque.
6. Implementar confirmacao de recebimento por protocolo.
7. Implementar auditoria de centro por verificador.

## Documento principal

- [PRD do MVP](docs/PRD_MVP_REDE_SOLIDARIA_BOT_TELEGRAM.md)

## Principios

- O banco e a fonte da verdade; o bot apenas conduz a conversa.
- Toda operacao operacional gera protocolo.
- Estoque nunca deve ser editado solto; ele muda por recebimento ou entrega.
- O MVP deve priorizar confianca, simplicidade e rastreabilidade.
- Fluxos longos devem ser salvos passo a passo para evitar perda de dados.
