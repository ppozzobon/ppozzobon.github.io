# Transição do Matlab para Python


Minha entrada no mundo da programação se iniciou com o Matlab durante a faculdade. O mesmo acontece com boa parte dos engenheiros mecânicos, eletricistas ou civis (basicamente qualquer engenharia que não as de software e computação).

A transição para python ocorreu após a universidade, quando busquei estudar o que era ciência de dados e machine learning. Hoje uso python também nas tarefas de engenharia, pois se mostrou um ambiente muito mais flexível e produtivo que o Matlab, com a vantagem adicional de ser open-source.

A curva de aprendizado para chegar em um nível em que você realmente é produtivo ao escrever código foi um pouco longa. O intuito dessa série de textos é mostrar alguns tópicos importantes nessa jornada, para que talvez a sua seja mais rápida.

## Bibliotecas

Uma das principais diferenças é que o ecossistema de python é fragmentado em bibliotecas. Cada biblioteca deverá ser instalada separadamente e importada no início da excução do código. Além disso, cada uma terá uma uma documentação própria, que pode variar em qualidade. No caso do Matlab todas as funções tem uma documentação consistente, que pode ser acessada diretamente da própria IDE.

As bibliotecas necessárias para desempenhar as tarefas mais comuns se você vem de um background de engenharia são:

- Numpy: álgebra linear
- Scipy: computação numérica (integração, interpolação e otimização, processamento de sinais e outros)
- matplotlib: gráficos
- statsmodels: estatística
- pandas: funções de dataframes (equivalentes às Tables do matlab), importação de arquivos de texto e excel
- simpy: matemática simbólica

Como a comunidade é muito grande certamente existem bibliotecas prontas para uma necessidade muito específica. Exemplos:

- [uncertainties](https://pythonhosted.org/uncertainties/): lida com cálculos de incerteza e propagação de erros
- [trusspy](https://github.com/adtzlr/trusspy) : análise estrutural de treliças

Tais recursos não são encontrados no Matlab ou suas toolboxes e podem ser um tremendo diferencial no aumento da produtividade de algumas tarefas.

## Instalação de bibliotecas

Em todo tutorial você verá 

```bash
pip install <nome da biblioteca>
```

**Não** instale bibliotecas dessa forma se você instalou seu ambiente python usando o Anaconda. Substitua por

```bash
conda install <nome da biblioteca>
```

Conda e pip são dois gerenciadores de pacotes distintos. Caso você utilize ambos simultaneamente você terá mais dificuldades em recriar o seu ambiente, além de estar mais sujeito à conflitos de versões. Essa "bagunça" de ambientes é a principal responsável do problema de um código executar sem problemas em uma máquina e não executa em outra. Quando se deparar com esse problema a primeira providência é garantir que as bibliotecas necessárias para a execução do código sejam as mesmas em ambas as máquinas ([leia sobre conda environments para saber como fazer isso](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#restoring-an-environment)).

## Organização e escrita do código

Uma das coisas que mais me incomodava no matlab era a impossibilidade de agrupar mais de uma função no mesmo arquivo de texto. Como resultado em algumas situações haviam dezenas de arquivos, muitas vezes com funções muito parecidas.

Em python não existe esse problema. Escreva suas funções em quantos arquivos de texto quiser. Para chamá-las, em outro arquivo, basta fazer uma importação. Digamos que você queira importar todo o conteúdo de um script chamado "utils", localizado dentro da pasta "lib". Basta fazer:

```python
import lib.utils as utils
```

Para as importações funcionarem é necessário criar um arquivo de nome \_\_init\_\_.py dentro da pasta lib. Esse arquivo pode ser vazio, ou possuir algum código, que será executado no momento da importação.

O caminho que você indicar para a importação do seu script é relativo a onde o processo do python está sendo executado. Para saber onde "está" o interpretador, faça:

```python
import os
os.cwd()
# cwd = current working directory
```

Caso se depare com algum erro durante a importação dos seus próprios scripts, leia sobre [absolute e relative imports](https://chrisyeh96.github.io/2017/08/08/definitive-guide-python-imports.html).

## Modo de uso

Existem duas "formas de usar" python: utilizando Jupyter Notebooks (equivalente ao Live Editor do Matlab) ou escrevendo e executando scripts.

Jupyter Notebooks é uma interface não apenas para executar python, mas outras linguagens também. Como vantagem é uma experiência de uso mais natural, principalmente se a tarefa é gerar gráficos e analisar dados, e a possibilidade de misturar texto, imagens e equações com código, facilitando o compartilhamento com usuários não técnicos.

A grande desvantagem é a impossibilidade de debugar o código, isto é, não há como "pausar" a execução do código na ocorrência de um erro para facilitar entender o que deu errado. Nesses casos uma alternativa é converter o jupyter notebook em um script comum e debugar o script.

## if \_\_init\_\_ == "\_\_name\_\_":

Você verá esse trecho de código em vários lugares. A função dele é verificar "onde" está ocorrendo a execuçao do código: se você está executando o script o resultado da condição é True. Caso outro arquivo chame a execução do script, a condição é False.

Um uso interessante é para você fazer pequenos testes com as funções escritas no seu script. Exemplo:

```python
def func1(x,y):
		return x + y

def func2(x,y):
	return x*y

if __init__ == "__name__":
	5 == func1(2,3)
	8 == func2(2,3)
```

Ao ser executado, esse script retorna True, True

## IDE

Minha recomendação de IDE é o [VS Code](https://code.visualstudio.com/). O VS Code é um editor de texto básico, que pode ter suas funcionalidades estendidas através de extensões, podendo assim ser transformado em uma IDE completa. Para isso instale a extensão Python e estará pronto para uso. 

Um dos recursos mais interessantes é o suporte nativo aos Jupyter Notebooks, não sendo necessário trocar a interface para usá-los.

---
Tem alguma dúvida? Lembra de algum tópico que demorou para enteder? Escreva nos comentários.
