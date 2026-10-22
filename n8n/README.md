# Assistente de Recomendação de Investimentos com RPA e n8n

Este projeto implementa um pipeline automatizado de Robotic Process Automation (RPA) utilizando o N8N integrado a modelos de Inteligência Artificial para análise de perfil financeiro e geração de recomendações personalizadas por e-mail.


## 1. Script Utilizado no RPA

Contém os nós de código JavaScript responsáveis pelo tratamento, parsing de valores monetários e normalização dos dados recebidos via Webhook e CSV.
Executa as regras de validação de negócios, cruzando o saldo disponível e o perfil do investidor com os produtos de investimento compatíveis.
Prepara e formata os dados em estruturas JSON prontas para alimentar o agente de IA e os serviços de mensageria subsequentes.


```Python 
  
import requests
from bs4 import BeautifulSoup

URL_PAGINA = "https://digitalinnovationone.github.io/dio-lab-assistente-investimentos-rpa-n8n/"
N8N_WEBHOOK = "https://harder-begun-signatures-she.trycloudflare.com/webhook-test/Clientes"  # ex: https://arthur123123.app.n8n.cloud/webhook/clientes

# 1) Baixar a página
html = requests.get(URL_PAGINA, timeout=30).text

# 2) Ler a tabela de clientes
soup = BeautifulSoup(html, "html.parser")
linhas = soup.select("#clientes tbody tr")
clientes = []
for linha in linhas:
    colunas = linha.find_all("td")
    cliente = {
        "nome": colunas[0].get_text(strip=True),
        "email": colunas[1].get_text(strip=True),
        "saldo": colunas[2].get_text(strip=True),
        "perfil": colunas[3].get_text(strip=True),
    }
    clientes.append(cliente)

print("Clientes encontrados:", len(clientes))
print("Exemplo 1º cliente:", clientes[0] if clientes else "nenhum")

# 3) Enviar pro n8n
payload = {"clientes": clientes}
resp = requests.post(
    N8N_WEBHOOK,
    json=payload,
    headers={"Content-Type": "application/json"},
    timeout=60
)

print("Status:", resp.status_code)
print("Headers:", dict(resp.headers))
print("Resposta (primeiros 1500 chars):")
print(resp.text[:1500])
```


## 2. Print do Fluxo em N8N

Apresenta o mapeamento visual completo do workflow, desde a captura inicial do Webhook até o disparo automatizado do e-mail via Gmail.
Destaca a integração entre nós de controle, mesclagem de fluxos de dados e o encadeamento dos modelos de linguagem com a Groq.

<img width="1366" height="612" alt="image" src="https://github.com/user-attachments/assets/85ba554d-68b5-42e9-b58c-0b6a7f1a6cb6" />

## 3. Documentação Explicando as Decisões Técnicas

O Groq foi adotado como provedor do modelo de IA por oferecer um plano gratuito adequado ao volume de testes do projeto, dispensando a inserção imediata de créditos pagos.


