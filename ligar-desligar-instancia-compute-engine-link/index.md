# Ligar e desligar instâncias do Google Compute Engine através de um link


<!--more-->

## Motivação
Sempre achei a interface do console do Google Cloud terrivelmente lenta. Em um projeto recente havia a necessidade de ligar e desligar constantemente uma máquina virtual, além de ativar/desativar regras de firewall. A quantidade de vezes em que via a tela de carregamento da interface no console já foi motivação suficiente para tentar automatizar essa tarefa.

{{< figure src="google-cloud-loading.png" title="Tela de carregamento do Google Cloud"  >}}

A solução encontrada foi fazer uma Cloud Function ativada por um request http (ou um link) que liga/desliga uma determinada máquina. Inicialmente procurei por uma solução mais simples, em que não fosse necessário nenhum tipo de código. Caso alguém conheça uma forma mais simples de fazer a mesma tarefa, por favor se manifeste nos comentários.

O código das funções pode ser visto em [meu Github](https://github.com/ppozzobon/cloud_funcs_utils).

## Criar nova conta de serviço
A conta de serviço é utilizada pela Cloud Function para interagir com os outros serviços do Google Cloud. Ela deve ter os privilégios suficientes para realizar as tarefas que a função precisa executar.

Procure pelo item IAM - Admin no Cloud Console, vá para contas de serviço e crie uma nova.

{{< figure src="iam.png" width="450" title="Caminho para criar a conta de serviço" >}}

Dê um nome e uma descrição para conta e vá para o próximo passo.

{{< figure src="create-service-account.png" width="450"  >}}

Defina um papel adequado para a conta. O papel é o que determina o que os serviços que usam essa conta podem ou não podem fazer. Como no caso pretendemos modificar configurações do Compute Engine, sugiro o papel Compute Admin.

{{< figure src="iam-roles.png" width="450" title="Selecione o papel adequado para a conta de serviço" >}}

O último passo não necessita de nenhuma configuração específica.

## Criar a Cloud Function

Para criar essa Cloud Function, procure esse serviço no console e ative-o, se necesssário. Crie uma nova função, dando um nome e uma região. No tipo de *trigger*, selecione http e permita chamadas não autenticadas. Isso é potencialmente falho do ponto de vista da segurança, pois qualquer um que conheça a URL e os parâmetros de invocação da função será capaz de ligar/desligar suas máquinas.

{{< figure src="function-trigger.png" width="450" title="Configurações de *triggering* (invocação) da função"  >}}

Clique em configurações avançadas e defina a conta de serviço que acabou de ser criada:

{{< figure src="cloud-function-service-account.png" width="450" title="Defina a conta de serviço a ser usada pela Cloud Funtion"  >}}

No próximo passo selecione a linguagem da função como python e defina o ponto de entrada para `main`. Copie e cole o código da função e do arquivo `requirements.txt`.

```python
import googleapiclient.discovery

compute = googleapiclient.discovery.build('compute', 'v1', cache_discovery=False)

def main(request):
    """Toggles compute engine instance start-stop
    """

    project = request.args.get('project')
    zone = request.args.get('zone')
    instance_name = request.args.get('instance')

    # get current state
    instance = compute.instances().get(project=project,
                                zone=zone,
                                instance=instance_name).execute()

    # decide command
    if instance['status'] == "TERMINATED":
        compute.instances().start(project=project,
                                zone=zone,
                                instance=instance_name).execute()

        return "{} turned on".format(instance_name)
    else:
        compute.instances().stop(project=project,
                                zone=zone,
                                instance=instance_name).execute()
        
        return "{} turned off".format(instance_name)
```

```
brotlipy==0.7.0
cachetools
certifi==2020.6.20
cffi==1.14.0
chardet==3.0.4
cryptography
google-api-core
google-api-python-client
google-auth
google-auth-httplib2==0.0.3
googleapis-common-protos==1.51.0
httplib2
idna
protobuf
pyasn1==0.4.8
pyasn1-modules==0.2.7
pycparser
pyOpenSSL==19.1.0
PySocks==1.7.1
pytz==2020.1
requests
rsa
simplejson
six
uritemplate==3.0.1
urllib3
```

{{< figure src="cloud-function.png" title="Configuração da linguagem e ponto de entrada"  >}}

## Uso
Para ativar a função basta agora chamá-la pelo seu link com os parâmetros necessários.

```
https://{endereço da função}.cloudfunctions.net/{nome da função}?project={project id}&zone={zona}&instance={nome da instância}
```

- endereço da função e nome da função: é o endereço fornecido no momento em que você [criou a Cloud Function](#criar-a-cloud-function)
- project id: pode ser visto no dashboard do seu projeto:
  {{< figure src="project-info.png" width="300" title="Encontrando a id do projeto"  >}}
- zona e nome da instancia: podem ser vistos na página de resumo do Compute Engine
  {{< figure src="instance-name.png" title="Encontrando a id do projeto"  >}}

Agora é só salvar os links nos favoritos e pronto, não será mais necessário logar no Cloud Console toda vez que precisar ligar ou desligar uma instância.
