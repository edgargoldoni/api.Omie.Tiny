# Integração entre Tiny e Omie para Processamento de Notas Fiscais

Este projeto tem como objetivo realizar a integração entre as APIs do **Tiny** e **Omie** para o processamento de notas fiscais, com foco na gestão de vendedores e na automação de envio de dados entre os sistemas. O código é responsável por consultar notas fiscais, processá-las e, se necessário, enviar informações para a Omie, além de realizar o gerenciamento e a limpeza de dados no formato XML.

Autor
Este projeto foi desenvolvido para Levits BPO Financeiro.

Para mais informações, visite o site oficial: Levits BPO Financeiro. https://levitsbpo.net/

## Sumário

- [Visão Geral](#visão-geral)
- [Pré-requisitos](#pré-requisitos)
- [Configuração Inicial](#configuração-inicial)
- [Funcionamento](#funcionamento)
- [Como Usar](#como-usar)
- [Detalhes das Funções](#detalhes-das-funções)
- [Considerações Finais](#considerações-finais)

## Visão Geral

Este script realiza as seguintes funções principais:

1. **Consulta e Processamento de Notas Fiscais**: O código consulta a API do Tiny para obter informações sobre as notas fiscais emitidas, processando os dados conforme necessário.
2. **Limpeza e Conversão de XML**: As notas fiscais são obtidas em formato XML e passam por um processo de limpeza e conversão, onde removem-se namespaces e caracteres especiais.
3. **Integração com a Omie**: Com as notas fiscais processadas, o script realiza a inclusão de novos vendedores na Omie, caso necessário, e envia os dados para o sistema Omie, associando os vendedores às notas fiscais.
4. **Logging Detalhado**: O sistema de logging do Python é configurado para registrar o progresso, erros e informações detalhadas do processo em arquivos de log, que são rotacionados para não exceder o tamanho máximo.

## Funcionamento

### Leitura e Filtragem das Notas Fiscais
O script começa fazendo uma pesquisa na API do Tiny para obter as notas fiscais geradas em um intervalo de tempo (configurado como 2 dias atrás).

### Verificação de Vendedores
Para cada nota fiscal, o script verifica se o vendedor está cadastrado no Omie. Se não estiver, o vendedor é adicionado automaticamente à plataforma.

### Processamento e Envio do XML para Omie
O XML da nota fiscal é limpo e convertido, gerando um hash MD5. Em seguida, o XML é enviado para a Omie junto com os dados necessários, como o ID do vendedor e a chave de acesso.

## Pré-requisitos

Certifique-se de ter as seguintes bibliotecas instaladas no seu ambiente:

- **requests**: Para realizar as requisições HTTP para as APIs.
- **logging**: Para registrar logs durante o processamento.
- **hashlib**: Para gerar o hash MD5 dos XMLs.
- **xml.etree.ElementTree**: Para manipulação de XMLs.

## Configuração Inicial

No início do script, você deve configurar algumas variáveis essenciais para que o sistema funcione corretamente:

### API do Tiny:

- `TINY_API_TOKEN`: O token de acesso à API do Tiny.

### API do Omie:

- `OMIE_API_KEY`: A chave da API Omie.
- `OMIE_API_SECRET`: O segredo da API Omie.

### Configurações de Log:

- `LOG_DIR`: O diretório onde os arquivos de log serão armazenados.
- `LOG_FILENAME_PREFIX`: O prefixo do nome dos arquivos de log.
- `LOG_MAX_BYTES`: O tamanho máximo do arquivo de log antes de realizar a rotação.
- `LOG_BACKUP_COUNT`: O número de backups de logs a serem mantidos.

### Exemplo de configuração inicial:

```python
# Credenciais da API do Tiny
TINY_API_TOKEN = 'seu_token_do_tiny'

# Credenciais da API da Omie
OMIE_API_KEY = 'sua_chave_da_omie'
OMIE_API_SECRET = 'seu_secret_da_omie'

# Configurações de Log
LOG_DIR = r"C:\Logs"  # Caminho para salvar os logs
LOG_FILENAME_PREFIX = "integração_tiny_omie"  # Prefixo dos arquivos de log
LOG_MAX_BYTES = 10 * 1024 * 1024  # Tamanho máximo dos arquivos de log (10MB)
LOG_BACKUP_COUNT = 5  # Número de backups a serem mantidos


Como Usar
Clone este repositório em sua máquina local.
Instale as dependências listadas em requirements.txt ou manualmente.
Configure as variáveis de ambiente ou as variáveis no script conforme as instruções acima.
Execute o script principal para integrar as APIs do Tiny e Omie.
Exemplo de uso do script:

import requests
import hashlib
import logging
from xml.etree import ElementTree as ET

# Função para configurar o log
def setup_logging():
    logging.basicConfig(
        level=logging.INFO,
        format='%(asctime)s - %(levelname)s - %(message)s',
        handlers=[
            logging.RotatingFileHandler(
                f'{LOG_DIR}/{LOG_FILENAME_PREFIX}.log', maxBytes=LOG_MAX_BYTES, backupCount=LOG_BACKUP_COUNT),
            logging.StreamHandler()
        ]
    )

# Função para buscar as notas fiscais do Tiny
def get_tiny_invoices():
    url = f'https://api.tiny.com.br/api2/notas_fiscais.pesquisar'
    params = {
        'token': TINY_API_TOKEN,
        'data_inicial': '2024-11-20',  # Data inicial (2 dias atrás)
        'data_final': '2024-11-22',    # Data final
    }
    response = requests.get(url, params=params)
    return response.json()

# Função para enviar XML para o Omie
def send_to_omie(xml_data, vendedor_id):
    url = 'https://app.omie.com.br/api/v1/nfe/nfse'
    data = {
        'nfe': xml_data,
        'vendedor_id': vendedor_id,
        'key': OMIE_API_KEY,
        'secret': OMIE_API_SECRET
    }
    response = requests.post(url, data=data)
    return response.json()

# Exemplo de chamada para o processo
if __name__ == '__main__':
    setup_logging()
    invoices = get_tiny_invoices()
    for invoice in invoices['notas_fiscais']:
        vendedor_id = invoice['vendedor_id']
        xml_data = invoice['xml']
        result = send_to_omie(xml_data, vendedor_id)
        logging.info(f'Nota fiscal enviada para o Omie: {result}')



###

Contribuições
Contribuições são bem-vindas! Se você encontrar algum erro ou tiver sugestões de melhorias, fique à vontade para abrir uma issue ou enviar um pull request.


