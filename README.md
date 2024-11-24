# Integração entre Tiny e Omie para Processamento de Notas Fiscais

Este projeto tem como objetivo realizar a integração entre as APIs do **Tiny** e **Omie** para o processamento de notas fiscais, com foco na gestão de vendedores e na automação de envio de dados entre os sistemas. O código é responsável por consultar notas fiscais, processá-las e, se necessário, enviar informações para a Omie, além de realizar o gerenciamento e a limpeza de dados no formato XML.

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

## Pré-requisitos

Antes de executar o código, é necessário garantir que o ambiente esteja preparado com os seguintes requisitos:

- **Python 3.6+**
- **Bibliotecas**:
  - `requests`: Para enviar requisições HTTP.
  - `json`: Para manipulação de dados JSON.
  - `xml.etree.ElementTree`: Para processamento de XML.
  - `hashlib`: Para geração de hash MD5.
  - `logging`: Para a configuração de logs.
  
Essas bibliotecas podem ser instaladas através do pip:




```bash
pip install requests


