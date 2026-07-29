# IRIS · Implantação de fornecedores

Sistema estático (HTML + CSS + JS puro, sem build e sem banco) para acompanhar treinamento e
adoção do app IRIS pelos fornecedores de remoção.

```
index.html    Consulta  — todo mundo vê, só leitura
admin.html    Admin     — cadastro, edição, exclusão e publicação
dados.json    A base    — única fonte de verdade
```

## Como os dados circulam

```
admin.html ──edita──> rascunho no navegador (localStorage)
                              │
                              ├── Publicar no GitHub ──> dados.json no repo ──> index.html lê
                              └── Baixar dados.json ──> você sobe o arquivo ──> index.html lê
```

A Consulta faz `fetch('dados.json')` a cada carregamento. Então:

- **Dentro do Admin** a edição é instantânea.
- **Para os outros**, vale o que estiver publicado no `dados.json`. Publicou, deu F5, apareceu.
  Não existe push automático porque não há servidor — é o preço de rodar sem banco.

Se o `dados.json` não puder ser lido (arquivo aberto direto do disco, via `file://`, ou fora do ar),
as duas páginas caem numa cópia embutida da planilha e avisam `dados embutidos` no topo.

## Subir no GitHub Pages

1. Novo repositório, joga os quatro arquivos na raiz.
2. Settings → Pages → Source: `Deploy from a branch`, branch `main`, pasta `/ (root)`.
3. Acesse `https://SEU-USUARIO.github.io/SEU-REPO/`.

## Publicar direto do Admin

No Admin, abra **Publicar direto no GitHub Pages** e preencha:

- **Repositório**: `usuario/nome-do-repo`
- **Caminho**: `dados.json`
- **Branch**: `main`
- **Token**: crie em GitHub → Settings → Developer settings → Personal access tokens →
  **Fine-grained tokens**. Selecione só este repositório e dê a permissão
  **Contents: Read and write**. Nada além disso.

O token fica no `localStorage` do seu navegador — nunca vai para o `dados.json` nem para o repositório.
Use isso na sua máquina, não em computador compartilhado. Se quiser trocar depois, apagar o campo e
salvar a configuração já resolve.

Depois de publicar, o Pages leva até ~1 minuto para servir o arquivo novo.

## Sem token, o caminho manual

**Baixar dados.json** no Admin → substitui o arquivo no repositório (pela interface do GitHub ou
`git commit`). Mesmo efeito, um passo a mais.

## Campos

Vieram da planilha, com dois derivados calculados na hora (mês de referência e dias desde o
treinamento — não precisam ser digitados) e um novo campo livre de observações.

A **situação** também é calculada: `Em uso`, `Em uso · web`, `Treinado · sem uso`,
o motivo do impedimento quando existe (`Recusa/Objeção`, `iOS Incompatível`, `Não Quis`, `Inativo`),
`Agendado` ou `Pendente`.

## Como ler as bolinhas

A bolinha grande, ao lado da situação, **pulsa quando o fornecedor já foi treinado** — verde se está
usando, laranja se treinou e ainda não usa.

As quatro bolinhas pequenas no rodapé do card são a trilha: Agendamento → Treinamento →
App instalado → Em uso. As duas primeiras acendem em **laranja** (implantação), as duas últimas em
**verde** (adoção de verdade). Quando o fornecedor usa pela web sem app, a última aparece só com o
contorno verde: pulou uma etapa, e isso é informação, não erro.

## Voltar para o Excel

**Baixar CSV** gera arquivo com `;` e BOM, abre direto no Excel em português com os mesmos
cabeçalhos da planilha original.

## Se um dia precisar de multiusuário

Duas pessoas editando ao mesmo tempo se sobrescrevem — a última publicação vence. Enquanto a edição
for sua, tudo bem. Quando não for, o caminho mais curto é trocar o `dados.json` por Supabase ou
Firebase mantendo as mesmas duas telas: só muda de onde vem a lista.
