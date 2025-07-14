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
4. Procurar nos logs por mais erros relacionados ao banco de dados e ou.
5. Verificar número de conexões e uso de recursos no RDS.
6. Valisar se há lentidão nas queries.
7. Validar string de conexão com o banco de dados.
8. Verificar no datadog por outras conexões com apis ou serviços externos.

## 2. Listar ferramentas e comandos que usaria.
`kubectl` - kubectl get pods para ver os pods que estão em execução ou restartando, kubectl describe pod para obter dados do liveness e readiness, kubectl logs para visualizar logs e kubectl top pod para obter consumo de recursos.

`datadog` - visualizar métricas da aplicação e banco de dados.

`dbeaver` ou outro cliente sql - para obter dados de queries em execução no banco de dados.

## 3. Apontar hipóteses prováveis sobre a causa.
1. Excesso de conexões no banco - Aumento do tráfego na aplicação devido a alguma campanha ou anormalidade, causando um maior número de conexões ao banco de dados.
2. Queries lentas no banco - alguma query que devido ao aumento de tráfego ou devido a alto comsumo de recursos no banco tornaram-se gargalo.
3. Intermitência de rede - Algum node ou pod do eks que não esteja conseguindo se comunicar corretamente com banco de dados.
4. Processamento interno da api ou dependências externas - aplicação pode abrir conexão com banco e realizar outras operações que estejam mais antes de efetuar commit estourando o erro de timeout.

## 4. Definir ações imediatas para mitigar o impacto.
1. Reiniciar pods - Encerra réplicas que podem não estar saudáveis e obter novo pool de conexão com banco de dados.
2. Ajustar min e max pool size na connection string - Caso esteja má configurado e banco de dados esteja atingindo máximo de conexões, adequar o tamanho do pool de conexões pode desonerar o banco de dados.
3. Aumentar quantidade de réplicas da aplicação - Caso seja constatado um aumento no processamento interno da api.
4. Aumentar o máximo de conexões no rds ou aumentar tamanho de instância - Caso esteja atingindo o máximo de conexões e aplicação já esteja bem configurada.
5. Limitar tráfego - Caso esteja ocorrendo alguma anormalidade, configurar rate-limit da aplicação.

## 5. Especificar como comunicaria o progresso do incidente.
Registro do incidente na ferramenta de itsm e comunicar no canal técnico, seguindo a forma abaixo a cada 15-30 minutos:

[Status Update - 12:15]

- Impacto: Latência da payment-api ainda acima de 2s.
- Ação em andamento: investigando pool de conexões e uso do RDS.
- Mitigação aplicada: reinício dos pods.
- Próximo passo: revisar métricas no banco e ajustar limite de conexões se necessário.

Novo update em: 12:45.

## 6. Documentar o incidente e as melhorias preventivas.

### Descrição
Data/hora do alerta
Descrição do alerta
Ações efetuadas
Tempo até mitigação 
Tempo até resolução

### Causa raiz (RCA)
Detalhar o que causou o problema, por exemplo, esgotamento de conexões no banco devido a falta de pool + pico de tráfego

### Impacto
Tempo de instabilidade
Pedidos perdidos/faturamento impactado

### Melhorias preventivas
Implementar pool de conexões (ou ajustar pool atual)
Monitorar número de conexões e uso do banco com alerta dedicado
Teste de carga automatizado semanalmente ou quinzenalmente

### Ação contínua
Análise de performance do banco
Código para fechar conexões corretamente
Limites de recursos revisados
