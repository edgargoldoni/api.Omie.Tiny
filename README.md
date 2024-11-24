# Integração entre Tiny e Omie - Automação de Envio de Notas Fiscais

Este projeto tem como objetivo automatizar o processo de integração entre dois sistemas de gestão: **Tiny** e **Omie**. Através deste sistema, as Notas Fiscais geradas no Tiny são enviadas para a plataforma Omie, permitindo que as empresas automatizem seus processos de vendas e contabilidade de maneira mais eficiente.

## Funcionalidade

O script realiza os seguintes passos:

1. **Consulta e Filtra Notas Fiscais no Tiny:** O sistema faz uma consulta na API do Tiny para buscar notas fiscais geradas dentro de um intervalo de data.
2. **Obtém Detalhes da Nota Fiscal:** Para cada nota fiscal, o sistema obtém detalhes como a chave de acesso, o nome do vendedor e o ID do vendedor.
3. **Verificação de Vendedores:** O sistema verifica se o vendedor associado à nota fiscal já está cadastrado na plataforma Omie. Caso não esteja, o sistema o inclui.
4. **Processamento do XML da Nota Fiscal:** O XML da nota fiscal é obtido do Tiny, limpo (removendo namespaces) e convertido para o formato adequado para o Omie.
5. **Envio para Omie:** O XML da nota fiscal é enviado para o Omie, junto com as informações adicionais, como a chave de acesso e o ID do vendedor.

## Como Funciona

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

Configuração
No início do script, você deve configurar algumas variáveis essenciais para que o sistema funcione corretamente:

API do Tiny:

TINY_API_TOKEN: O token de acesso à API do Tiny.
API do Omie:

OMIE_API_KEY: A chave da API Omie.
OMIE_API_SECRET: O segredo da API Omie.
Configurações de Log:

LOG_DIR: O diretório onde os arquivos de log serão armazenados.
LOG_FILENAME_PREFIX: O prefixo do nome dos arquivos de log.
LOG_MAX_BYTES: O tamanho máximo do arquivo de log antes de realizar a rotação.
LOG_BACKUP_COUNT: O número de backups de logs a serem mantidos.
Exemplo de configuração inicial:

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


Contribuições
Contribuições são bem-vindas! Se você encontrar algum erro ou tiver sugestões de melhorias, fique à vontade para abrir uma issue ou enviar um pull request.

Autor
Este projeto foi desenvolvido para Levits BPO Financeiro.

Para mais informações, visite o site oficial: Levits BPO Financeiro. https://levitsbpo.net/
