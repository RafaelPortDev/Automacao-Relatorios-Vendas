# 📊 Automação de Indicadores (OnePage das Lojas)

Automação em Python que calcula os indicadores diários e anuais de uma rede de lojas, monta o **OnePage** de cada loja e envia tudo por e-mail (via Gmail API) para os gerentes e para a diretoria, sem nenhum trabalho manual.

---

## 📑 Sumário

- [Sobre o projeto](#-sobre-o-projeto)
- [Funcionalidades](#-funcionalidades)
- [Como funciona](#-como-funciona)
- [Indicadores e metas](#-indicadores-e-metas)
- [Tecnologias](#-tecnologias)
- [Estrutura do repositório](#-estrutura-do-repositório)
- [Pré-requisitos](#-pré-requisitos)
- [Instalação](#-instalação)
- [Configuração do Gmail](#-configuração-do-gmail)
- [Dados de entrada](#-dados-de-entrada)
- [Como executar](#-como-executar)
- [Arquivos gerados](#-arquivos-gerados)
- [Observações](#-observações)

---

## 📌 Sobre o projeto

Em uma rede de lojas, a equipe de análise de dados precisa calcular todo dia os indicadores de cada unidade e enviá-los aos responsáveis. Um **OnePage** é um resumo direto, em uma única página, que mostra quais metas a loja cumpriu ou não e permite comparar o desempenho entre lojas.

Este projeto automatiza todo esse processo: lê as bases de vendas, calcula os indicadores de cada loja, gera o backup das planilhas, envia o OnePage no corpo do e-mail de cada gerente (com a planilha da loja em anexo) e, por fim, envia à diretoria os rankings de faturamento do dia e do ano.

## ✨ Funcionalidades

- Leitura e cruzamento das bases de **vendas**, **lojas** e **e-mails**.
- Separação dos dados por loja: cada gerente recebe apenas as informações da sua própria loja.
- Cálculo dos indicadores **do dia** (data mais recente da base) e **do ano**.
- Comparação com as metas, com sinalização em **verde** (meta batida) ou **vermelho** (meta não batida).
- Backup automático das planilhas de cada loja, organizado por pasta e por data.
- E-mail em HTML para cada gerente, com o OnePage no corpo e a planilha da loja em anexo.
- E-mail para a diretoria com os rankings do dia e do ano em anexo, destacando a melhor e a pior loja de cada período.

## 🔄 Como funciona

1. **Carrega** as bases `Emails.xlsx`, `Lojas.csv` e `Vendas.xlsx`.
2. **Relaciona** as vendas às lojas pelo `ID Loja`.
3. **Separa** as vendas de cada loja e identifica a data mais recente disponível.
4. **Salva o backup** da planilha de cada loja na pasta correspondente.
5. **Autentica** no Gmail pela API do Google.
6. **Calcula os indicadores** de cada loja, define as cores conforme as metas e monta o e-mail em HTML.
7. **Envia** o OnePage e a planilha em anexo ao gerente de cada loja.
8. **Gera os rankings** de faturamento (dia e ano) e envia à diretoria.

## 🎯 Indicadores e metas

| Indicador | Descrição | Meta Dia | Meta Ano |
|---|---|---|---|
| Faturamento | Soma do valor final das vendas | R$ 1.000 | R$ 1.650.000 |
| Diversidade de Produtos | Quantidade de produtos diferentes vendidos | 4 | 120 |
| Ticket Médio | Valor médio por venda (agrupado por código de venda) | R$ 500 | R$ 500 |

> O indicador do **dia** considera a data mais recente da planilha de vendas. O indicador do **ano** considera todo o período disponível na base.
> As metas ficam definidas em variáveis no início da etapa de indicadores do notebook e podem ser alteradas facilmente.

## 🛠 Tecnologias

- [Python 3](https://www.python.org/)
- [Jupyter Notebook](https://jupyter.org/)
- [pandas](https://pandas.pydata.org/) e [openpyxl](https://openpyxl.readthedocs.io/) para manipulação e exportação de planilhas
- [Gmail API](https://developers.google.com/gmail/api) (`google-api-python-client`, `google-auth`, `google-auth-oauthlib`) para o envio dos e-mails
- Bibliotecas padrão: `pathlib`, `base64`, `os` e `email`

## 📁 Estrutura do repositório

```
.
├── Automacao de Processo.ipynb   # Notebook com todo o processo automatizado
├── Bases de Dados/               # Bases de entrada (veja "Dados de entrada")
│   ├── Emails.xlsx
│   ├── Lojas.csv
│   └── Vendas.xlsx
├── Backup Arquivos Lojas/        # Gerada automaticamente na execução
│   ├── <Nome da Loja>/
│   │   └── <mês>_<dia>_<Nome da Loja>.xlsx
│   ├── <mês>_<dia>_Ranking Anual.xlsx
│   └── <mês>_<dia>_Ranking Dia.xlsx
├── credentials.json              # NÃO versionado (credenciais da API do Google)
├── token.json                    # NÃO versionado (gerado após a autenticação)
└── README.md
```

## ✅ Pré-requisitos

- Python 3 e Jupyter Notebook (ou JupyterLab / VS Code com suporte a notebooks)
- Uma conta Google com a **Gmail API** habilitada
- O arquivo `credentials.json` do seu projeto no Google Cloud (veja a próxima seção)

## ⚙ Instalação

```bash
# Clone o repositório
git clone https://github.com/RafaelPortDev/Automacao-Relatorios-Vendas.git
cd Automacao-Relatorios-Vendas

# (Opcional) crie e ative um ambiente virtual
python -m venv venv
venv\Scripts\activate        # Windows
# source venv/bin/activate   # Linux/macOS

# Instale as dependências
pip install pandas openpyxl notebook google-api-python-client google-auth google-auth-oauthlib
```

## 🔐 Configuração do Gmail

O envio dos e-mails usa a Gmail API com OAuth e o escopo `gmail.send` (somente envio). Dois arquivos são necessários:

| Arquivo | O que é | Como obter |
|---|---|---|
| `credentials.json` | Credenciais OAuth do aplicativo (client secrets) | Criado no [Google Cloud Console](https://console.cloud.google.com/): habilite a Gmail API e crie uma credencial **OAuth client ID** do tipo *Desktop app* |
| `token.json` | Autorização salva do usuário | Gerado automaticamente na primeira execução, após você autorizar o acesso no navegador |

Coloque o `credentials.json` na **mesma pasta do notebook**. Na primeira execução, uma janela do navegador será aberta para você autorizar o acesso; depois disso o `token.json` é criado e reutilizado (e renovado) nas próximas execuções.

> ⚠️ Esses dois arquivos **não estão neste repositório** e nunca devem ser publicados.

## 🗂 Dados de entrada

As bases devem ficar na pasta `Bases de Dados/`:

| Arquivo | Conteúdo esperado |
|---|---|
| `Emails.xlsx` | Colunas `Gerente`, `Loja` e `E-mail`, com uma linha por gerente e uma linha com `Loja` igual a `Diretoria` |
| `Lojas.csv` | Separador `;` e codificação UTF-8, com as colunas `ID Loja` e `Loja` |
| `Vendas.xlsx` | Vendas de todas as lojas, com as colunas `Data`, `ID Loja`, `Código Venda`, `Produto` e `Valor Final` |

> 💡 **Dica para testes:** antes da primeira execução real, substitua os e-mails da planilha `Emails.xlsx` por um e-mail seu. Assim você confere o resultado sem enviar mensagens aos gerentes de verdade.

## ▶ Como executar

1. Garanta que `credentials.json` e a pasta `Bases de Dados/` estão no mesmo diretório do notebook.
2. Abra o notebook:
   ```bash
   jupyter notebook "Automacao de Processo.ipynb"
   ```
3. Execute as células em ordem (**Kernel → Restart & Run All**).
4. Na primeira vez, autorize o acesso ao Gmail no navegador.
5. Acompanhe no final de cada etapa as mensagens de confirmação, como `E-mail da Loja <nome> enviado` e `E-mail da Diretoria enviado pelo Gmail`.

## 📦 Arquivos gerados

- **Backup por loja:** `Backup Arquivos Lojas/<Loja>/<mês>_<dia>_<Loja>.xlsx`, que cria um histórico de planilhas por data.
- **Rankings:** `<mês>_<dia>_Ranking Anual.xlsx` e `<mês>_<dia>_Ranking Dia.xlsx`, ordenados do maior para o menor faturamento.
- **E-mails enviados:** um por gerente (OnePage + planilha da loja) e um para a diretoria (melhor e pior loja do dia e do ano + os dois rankings).

## 📝 Observações

- Os caminhos das pastas no notebook usam barra invertida (`Bases de Dados\Emails.xlsx`), portanto o código foi escrito para **Windows**. Em Linux/macOS, ajuste os caminhos (por exemplo, usando `pathlib`).
- Os e-mails são enviados pela conta Google autenticada (`userId='me'`).
- O e-mail de cada gerente leva apenas a planilha da própria loja, o que evita compartilhar dados entre lojas.
- Rodar o notebook mais de uma vez no mesmo dia reenvia os e-mails e sobrescreve os arquivos de backup daquela data.
