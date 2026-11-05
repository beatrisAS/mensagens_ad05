<div align="center">

# ✉️ Mensagens AD05

### Python + Google Apps Script + Google Sheets

**Uma integração simples para enviar e consultar mensagens por HTTP**

Notebook didático que demonstra como um cliente Python pode conversar com um Web App do Google Apps Script para gravar e ler mensagens armazenadas em uma planilha.

[![Python](https://img.shields.io/badge/Python-3-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![Google Colab](https://img.shields.io/badge/Google%20Colab-notebook-F9AB00?logo=googlecolab&logoColor=white)](https://colab.research.google.com/)
[![Google Apps Script](https://img.shields.io/badge/Google%20Apps%20Script-Web%20App-4285F4?logo=google&logoColor=white)](https://developers.google.com/apps-script)
[![Google Sheets](https://img.shields.io/badge/Google%20Sheets-armazenamento-34A853?logo=googlesheets&logoColor=white)](https://developers.google.com/workspace/sheets/api)
[![HTTP](https://img.shields.io/badge/HTTP-GET%20%7C%20POST-6C63FF)](https://developer.mozilla.org/pt-BR/docs/Web/HTTP)
[![Abrir no Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/beatrisAS/mensagens_ad05/blob/main/api/mensagens.ipynb)

</div>

---

## 📌 Visão geral

Este projeto demonstra uma integração entre **Python**, **Google Apps Script** e **Google Sheets**. O notebook `api/mensagens.ipynb` atua como cliente HTTP: envia mensagens com nome e e-mail usando uma requisição **POST**, e consulta as mensagens com **GET**.

O Google Apps Script, publicado como Web App, recebe as requisições e serve de camada intermediária para uma planilha. No fluxo demonstrado, o envio retorna um texto de confirmação e a consulta retorna uma lista JSON de registros.

> **Escopo:** o repositório contém o notebook cliente. O código-fonte do Google Apps Script e a estrutura da planilha não estão incluídos. Para executar a integração, é preciso ter acesso a um Web App compatível com o formato de requisição e resposta descrito aqui.

---

## 🎯 Objetivos de aprendizagem

Com este notebook, é possível praticar:

- chamadas HTTP em Python usando a biblioteca `requests`;
- diferenças entre requisições **POST** (envio de dados) e **GET** (consulta);
- envio de objetos JSON em uma requisição;
- leitura e formatação de uma resposta JSON;
- organização de funções reutilizáveis para um e vários registros;
- integração entre um cliente Python, um endpoint Web App e uma planilha;
- cuidados básicos ao compartilhar dados pessoais e endpoints públicos.

---

## 🛠️ Tecnologias

| Tecnologia | Função no projeto |
| --- | --- |
| **Python 3** | Implementa o cliente que envia e consulta mensagens. |
| **Google Colab** | Ambiente no qual o notebook foi preparado para ser executado. |
| **requests** | Envia requisições HTTP POST e GET e interpreta a resposta JSON. |
| **Google Apps Script** | Atua como Web App que recebe as chamadas e se comunica com a planilha. |
| **Google Sheets** | Armazena as linhas e fornece os dados consultados pelo Apps Script. |
| **json** | Formata os dados JSON de maneira legível no notebook. |

O notebook informa que o Colab já disponibiliza Python e `requests`, portanto não inclui uma etapa de instalação de dependências.

---

## 📁 Estrutura do repositório

```text
mensagens_ad05/
│
├── README.md
│
└── api/
    └── mensagens.ipynb
```

| Arquivo | Descrição |
| --- | --- |
| [`api/mensagens.ipynb`](api/mensagens.ipynb) | Notebook com configuração do endpoint, funções POST e GET e exemplos de execução. |
| `README.md` | Documentação do repositório e instruções de uso. |

---

## 🏗️ Arquitetura

```text
┌───────────────────────────┐
│ Notebook Python no Colab  │
│                           │
│ requests.post()           │
│ requests.get()            │
└─────────────┬─────────────┘
              │ HTTPS
              ▼
┌───────────────────────────┐
│ Google Apps Script        │
│ Web App                   │
│                           │
│ POST → doPost             │
│ GET  → doGet              │
└─────────────┬─────────────┘
              │
              ▼
┌───────────────────────────┐
│ Google Sheets             │
│ Data | Nome | Email       │
└───────────────────────────┘
```

### Fluxo de envio

1. O notebook cria um objeto com os campos `nome` e `email`.
2. `requests.post()` envia o objeto como JSON para `API_URL`.
3. O Apps Script recebe a chamada POST — o notebook espera que o Web App trate esse pedido em `doPost` e grave uma nova linha.
4. O notebook imprime o texto retornado pelo endpoint.

### Fluxo de consulta

1. `requests.get()` envia uma chamada GET para `API_URL`.
2. O notebook interpreta a resposta com `resposta.json()`.
3. Os dados são exibidos com indentação e preservação de caracteres acentuados.
4. A função de consulta da última mensagem seleciona o último elemento da lista recebida.

---

## 🔌 Contrato esperado da API

O notebook pressupõe que o endpoint implantado aceite os seguintes formatos. Como o código do Apps Script não faz parte deste repositório, este é o **contrato esperado pelo cliente**, não uma especificação validada de toda a implementação do servidor.

### Enviar uma mensagem — POST

**Endereço:** valor de `API_URL`, terminado em `/exec`.

**Corpo JSON esperado:**

```json
{
  "nome": "Alice",
  "email": "alice@example.com"
}
```

O notebook espera uma resposta textual semelhante a:

```text
Sucesso
```

A gravação exemplificada armazena data, nome e e-mail; a data é produzida pelo lado do Apps Script, de acordo com os dados exibidos no notebook.

### Consultar mensagens — GET

A função GET espera receber um JSON em forma de lista, por exemplo:

```json
[
  {
    "Data": "2026-11-04T18:17:59.000Z",
    "Nome": "Alice",
    "Email": "alice@example.com"
  }
]
```

Os nomes das propriedades retornadas refletem os cabeçalhos da planilha do exemplo: `Data`, `Nome` e `Email`. A capitalização desses campos é diferente das chaves em minúsculas enviadas no POST (`nome` e `email`).

> Para a consulta da última mensagem, o cliente considera que a resposta é uma lista ordenada da mais antiga para a mais recente. Confirme a ordenação no Apps Script; o notebook não a valida.

---

## 🧩 Funções do notebook

| Função | Método HTTP | Descrição |
| --- | --- | --- |
| `enviar_mensagem(nome, email)` | POST | Envia um registro JSON e imprime o corpo textual da resposta. |
| `enviar_varias_mensagens(mensagens)` | POST, uma chamada por item | Percorre uma lista de dicionários e envia cada mensagem individualmente. |
| `obter_todas_mensagens()` | GET | Lê a resposta JSON e imprime a lista formatada. |
| `obter_ultima_mensagem()` | GET | Busca a lista e imprime o último item; se não houver itens, informa que nenhuma mensagem foi encontrada. |

### Formato da lista para envio em lote

`enviar_varias_mensagens()` não envia um único lote ao servidor: percorre a lista e chama `enviar_mensagem()` **uma vez por mensagem**.

```python
mensagens = [
    {"nome": "Alice", "email": "alice@example.com"},
    {"nome": "Bruno", "email": "bruno@example.com"},
]

enviar_varias_mensagens(mensagens)
```

---

## 🚀 Como executar

### Pré-requisitos

- Uma conta Google com acesso ao Google Colab.
- Um Google Apps Script publicado como Web App que aceite POST e GET no formato descrito acima.
- Uma planilha Google acessível ao script, configurada com os cabeçalhos necessários.
- A URL de implantação do Web App, normalmente terminada em `/exec`.
- Permissão para usar esse endpoint e gravar/consultar os dados de teste.

### 1. Abra o notebook

Use o botão **Abrir no Colab** no início deste README ou acesse diretamente:

[![Abrir `mensagens.ipynb` no Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/beatrisAS/mensagens_ad05/blob/main/api/mensagens.ipynb)

Se quiser preservar o original, crie uma cópia do notebook na sua conta antes de editar.

### 2. Confira o Web App e a planilha

Antes de executar o cliente, confirme que:

1. O projeto Apps Script está ligado à planilha correta.
2. O Web App foi implantado e está acessível para a forma de autenticação escolhida.
3. O endpoint trata POST e GET como o notebook espera.
4. A planilha tem os cabeçalhos que o servidor usa. O resultado do exemplo usa `Data`, `Nome` e `Email`.
5. Você está usando dados fictícios de teste ou possui autorização para enviar os dados.

As etapas de criação do Apps Script, implantação e configuração da planilha **não estão descritas nem automatizadas neste repositório**; consulte a documentação oficial e as políticas da conta Google utilizada.

### 3. Configure `API_URL`

Na primeira célula de código, substitua a URL existente pela URL da **sua própria implantação**:

```python
API_URL = "https://script.google.com/macros/s/SEU_ID_DE_IMPLANTACAO/exec"
```

Não é necessário alterar o restante do código, desde que seu Web App aceite o mesmo contrato. O notebook atual contém uma URL de implantação preenchida: revise-a e troque-a por um endpoint sob seu controle antes de executar ou compartilhar a cópia.

### 4. Execute as células na ordem

Execute o notebook de cima para baixo, usando o botão de execução de cada célula ou `Shift + Enter`.

| Etapa | Ação |
| --- | --- |
| Configuração | Importa `json` e `requests` e define `API_URL`. |
| Funções de envio | Define o envio de uma mensagem e a iteração por várias mensagens. |
| Funções de consulta | Define a leitura de todos os registros e do último item. |
| Envio de teste | Envia uma mensagem fictícia de exemplo. |
| Envio de vários registros | Envia as mensagens de exemplo, uma por chamada. |
| Consulta completa | Busca e mostra a lista de mensagens retornada. |
| Consulta da última | Mostra somente o último item da lista. |

### 5. Use dados de teste seguros

Os exemplos do notebook usam nomes fictícios e endereços do domínio reservado `example.com`. Mantenha dados de teste enquanto verifica a integração; não substitua por informações pessoais reais sem autorização e necessidade legítima.

---

## 🧪 Exemplos de uso

### Enviar uma mensagem

```python
enviar_mensagem("Alice", "alice@example.com")
```

O notebook imprime o corpo da resposta. No exemplo salvo, o servidor respondeu `Sucesso`.

### Enviar várias mensagens

```python
mensagens = [
    {"nome": "Bruno", "email": "bruno@example.com"},
    {"nome": "Camila", "email": "camila@example.com"},
]

enviar_varias_mensagens(mensagens)
```

A função faz uma chamada POST para cada item e imprime uma resposta para cada envio.

### Consultar todos os registros

```python
obter_todas_mensagens()
```

A função imprime o JSON retornado pelo servidor com recuo de dois espaços e sem escapar caracteres acentuados.

### Consultar o último registro

```python
obter_ultima_mensagem()
```

Se a lista estiver vazia, a função imprime:

```text
Nenhuma mensagem encontrada.
```

---

## ✅ Resultado esperado

No notebook verificado, os exemplos de envio retornam `Sucesso`. A consulta retorna registros com campos equivalentes a:

```json
{
  "Data": "2026-11-04T18:18:24.000Z",
  "Nome": "charlie",
  "Email": "charlie@example.com"
}
```

Os registros vistos na saída salva são exemplos de execução, não uma garantia de que a planilha ou o endpoint permaneçam iguais. Cada nova execução pode acrescentar outros registros à planilha conectada.

---

## 🔐 Segurança e privacidade

O projeto manipula **nomes e endereços de e-mail**, que podem identificar pessoas. Além disso, uma URL de Web App pode permitir operações dependendo das permissões e da lógica implementadas no Apps Script.

- **Revise e substitua a URL já preenchida no notebook** por um endpoint que você administra ou está autorizada a utilizar.
- Não envie nomes, e-mails ou outros dados pessoais reais em testes públicos ou sem finalidade e autorização adequadas. Prefira dados fictícios.
- Verifique as opções de acesso e execução escolhidas na implantação do Apps Script. A URL, por si só, não comprova que o endpoint seja seguro.
- Não publique conteúdo da planilha nem respostas que revelem informações pessoais.
- Não considere este notebook uma camada de autenticação, autorização ou validação: ele envia dados ao endpoint e imprime a resposta.
- Restrinja o acesso à planilha e ao projeto Apps Script ao mínimo necessário.
- Antes de tornar o repositório público, confira as células de código e as saídas salvas. Não deixe dados pessoais, URLs privadas ou informações de acesso em arquivos versionados.
- Se uma URL de implantação exposta permitir escrita ou leitura não autorizada, revise a implantação e as permissões no Apps Script e substitua/revogue a implantação conforme as opções disponíveis na sua conta.

> **Importante:** o Apps Script não está incluído neste repositório. A validação de entradas, o controle de acesso, a proteção contra abuso e a política de retenção dependem da implementação e configuração do servidor.

---

## ⚠️ Limitações conhecidas

O notebook atual é uma demonstração didática, não um serviço de produção. Entre os limites visíveis no código:

- Não configura nem implanta o projeto Apps Script.
- Não cria a planilha nem valida os cabeçalhos antes do envio.
- Não chama `raise_for_status()` nem trata explicitamente erros HTTP, indisponibilidade, tempo limite ou respostas em formato inesperado.
- Imprime o texto da resposta POST, sem validar que o servidor confirmou a gravação de forma confiável.
- Envia várias mensagens em requisições separadas; uma falha pode interromper a sequência e não há mecanismo de repetição ou resumo de sucesso/falha.
- Não valida se `nome` ou `email` estão vazios ou seguem um formato esperado.
- A busca da última mensagem usa o último elemento retornado; depende da ordem fornecida pelo endpoint.
- A consulta imprime todos os registros devolvidos pelo servidor; para uma planilha extensa, isso pode produzir respostas muito grandes.
- Não implementa autenticação do usuário final no próprio cliente.

---

## 🧰 Solução de problemas

| Sintoma | O que verificar |
| --- | --- |
| A configuração imprime o aviso para trocar a URL | Confirme que `API_URL` é a URL completa da sua implantação, começando por `https://script.google.com/` e terminando em `/exec`. |
| A requisição retorna erro ou página HTML | Confira se a implantação está ativa, se a URL corresponde ao Web App e se o acesso está autorizado. Uma página de login ou erro não é uma resposta JSON da API. |
| POST não retorna `Sucesso` | Verifique o `doPost` no Apps Script, a planilha de destino, o formato JSON e as permissões de edição. O texto `Sucesso` é a resposta do exemplo, não uma validação feita pelo cliente. |
| GET gera erro ao executar `resposta.json()` | Confira se o endpoint responde JSON válido, sem texto extra, página HTML de login ou mensagem de erro antes do JSON. |
| A consulta retorna lista vazia | Verifique se os envios chegaram à planilha correta e se o Apps Script está lendo a aba esperada. |
| A última mensagem não parece ser a mais recente | Confira a ordenação da resposta GET no servidor; o notebook seleciona apenas `dados[-1]`. |
| Algumas mensagens de um envio em lote não aparecem | Como cada mensagem é enviada separadamente, confira a resposta de cada chamada e os registros gravados na planilha. |
| O notebook usa uma planilha ou endpoint inesperado | Substitua `API_URL` e confirme no Apps Script qual planilha está configurada como destino. |

---

## 💡 Possíveis melhorias

Estas são ideias para evoluir o exercício; **não estão implementadas na versão atual**:

- Adicionar `timeout`, `raise_for_status()` e tratamento de erros de rede e HTTP.
- Validar os campos antes do envio e padronizar o formato das respostas.
- Retornar um resumo por item em envios múltiplos, indicando sucesso ou falha sem ocultar erros.
- Implementar paginação ou filtros na consulta para evitar carregar toda a planilha em cada GET.
- Definir autenticação e autorização no servidor; evitar endpoints de escrita acessíveis publicamente sem controles apropriados.
- Separar configurações e funções em módulos, caso o projeto cresça além do notebook.
- Adicionar testes com respostas simuladas para não gravar em uma planilha real durante o desenvolvimento.
- Documentar, versionar e testar separadamente o código Apps Script, se ele fizer parte do escopo futuro.
- Incluir um modelo de dados e regras de validação para data, nome e e-mail.

---

## 📚 Referências

- [Documentação do Python em português](https://docs.python.org/pt-br/3/)
- [Biblioteca `requests`](https://requests.readthedocs.io/en/latest/)
- [Requisições HTTP — MDN em português](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Methods)
- [Google Apps Script — documentação](https://developers.google.com/apps-script?hl=pt-br)
- [Web Apps no Google Apps Script](https://developers.google.com/apps-script/guides/web?hl=pt-br)
- [Google Sheets API](https://developers.google.com/workspace/sheets/api?hl=pt-br)
- [Google Colab](https://colab.research.google.com/)

---

## 👩‍💻 Autoria

**Beatris Antunes Silva**

---

<div align="center">
  Projeto de estudo sobre integração entre Python, HTTP e serviços Google no contexto do bootcamp de Analista de Dados da Generation Brasil.
</div>
