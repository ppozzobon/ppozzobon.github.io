# Como produzir um relatório a partir de um Jupyter Notebook


Um problema comum é como compartilhar Jupyter Notebooks com usuários não-técnicos. Das várias soluções disponíveis, a que achei mais simples até o momento é exportar os notebooks como html usando o [nbconvert](https://nbconvert.readthedocs.io/en/latest/index.html). O formato html é interessante pois com ele é possível preservar conteúdo interativo, como os gráficos gerados pelo `plotly`, por exemplo.

Para utilizar o nbconvert, basta usar o comando

```
jupyter nbconvert <path-to-notebook> --to html
```

É possível customizar o template utilizado para fazer a conversão. Podemos, por exemplo, adicionar uma logomarca no cabeçalho dos notebooks e um texto padrão de rodapé.


{{< admonition type=tip open=true >}}
As instruções a seguir se aplicam para versão do 6.0.7 ou superior
{{< /admonition >}}

Vamos criar um novo template e adicionar as modificações. Criar uma pasta com o nome desejado do template, em nosso exemplo `<template-name>`. Essa pasta deve estar dentro de um dos diretórios esperados pelo nbconvert. Para consultar os diretórios permitidos, usar o comando:

```
jupyter --paths
```

Em meu caso, escolhi `~/miniconda3/envs/<env-name>/share/jupyter/nbconvert/templates`.


Criar dois arquivos dentro dessa pasta: `conf.json` e `index.html.j2`.

O arquivo `conf.json`:

```json
{
    "base_template": "lab",
    "mimetypes": {
        "text/html": true
    },
    "preprocessors": {
        "100-pygments": {
            "type": "nbconvert.preprocessors.CSSHTMLHeaderPreprocessor",
            "enabled": true
        }
    }
}
```

O significado desse arquivo é que estamos usando como base o template chamado `lab`. O que faremos será apenas adicionar conteúdo a esse template.

O arquivo `index.html.j2` trata-se de um template jinja. Abaixo um exemplo de arquivo que adiciona uma logomarca no cabeçalho da página. Note que a imagem está codificada em [base64](https://www.base64-image.de/). Nesse template também podemos adicionar estilos css à página. No exemplo adicionamos uma linha na lateral esquerda das células de texto, para dar mais destaque a elas em meio às células de código.

```html
{%- extends 'lab/index.html.j2' -%}

{%- block html_head_css -%}
  {{ super() }}
  <style>
    .header {
      overflow: hidden;
      padding-top: 20%;
    }
    .jp-Notebook{
    overflow: auto;
    background-image: url(data:<conteúdo-da-imagem>);
    background-repeat: no-repeat;
    height: 200px;
    width: auto;
    background-position: left;
    background-size: 100%;
    }
    /* Overrides of notebook CSS for static HTML export */
    .jp-MarkdownOutput {
    margin-top:-2px;
    margin-bottom:-2px;
    padding-top:2px;
    padding-bottom:2px;
    border-left:2px solid #e1dcdc;
    border-collapse:collapse;
    border-top:none;
    border-bottom:none;
    }
    .custom-footer{
    display: block;
    margin-left: auto;
    margin-right: auto;
    text-align: center;
    font-size: x-small;
    }
  </style>
{%- endblock html_head_css -%}

{% block body_header %}
  <body class="jp-Notebook theme-light" style="padding: 0; margin: 0;">
  <div class="header">
  </div>
  <div style="padding: 20px; margin: 20px;">
{% endblock body_header %}

{% block body_footer %}
  <!-- </div> -->
  	<div class="custom-footer">
      <hr>
	    <p>Este documento é propriedade de XYZ.</p>
	  </div>
	</div>
  </body>
{% endblock body_footer %}
```

Depois de tudo configurado, basta usar:

```
jupyter nbconvert <path-to-notebook> --to html --template <template-name>
```
