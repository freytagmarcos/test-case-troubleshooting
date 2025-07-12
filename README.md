# Desafio 2 - Exercício de Troubleshooting



Após um lançamento bem-sucedido e um ano de operação estável sem incidentes, a plataforma de e-commerce enfrenta seu primeiro problema. Em uma segunda-feira, logo após você assumir o plantão, um alerta crítico é disparado no Datadog:

ALERTA: Latência média da API acima de 2 segundos por 10 minutos.
Ambiente: Produção
Cluster: EKS-prod
Namespace: payment
Serviço impactado: payment-api

Observações:

O time de desenvolvimento relata que não houve mudanças de código nas últimas 24h.
O ArgoCD mostra o Helm release como Healthy e Synced.
O workload roda 6 réplicas.
Nos logs recentes, algumas requisições mostram timeout ao acessar o banco RDS.
O serviço payment-api é responsável pelo processamento de pagamentos, e sua indisponibilidade impacta diretamente o faturamento da empresa.

## 1. Descrever a sequência de passos que realizaria para diagnosticar o problema.
1. Verificar ficar se todos as réplicas da aplicação estão Running e/ou se há algum pod reiniciando ou em crash. 
2. Verificar no datadog se aplicação teve crescimento no tráfego.
3. Verificar consumo de memória e cpu dos pods da aplicação.
Listar ferramentas e comandos que usaria.
Apontar hipóteses prováveis sobre a causa.
Definir ações imediatas para mitigar o impacto.
Especificar como comunicaria o progresso do incidente.
Documentar o incidente e as melhorias preventivas.
