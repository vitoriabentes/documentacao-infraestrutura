# Infraestrutura Nginx

O Nginx atua como **proxy reverso** e **ponto único de entrada** para todos os microsserviços da plataforma. Centralizando o roteamento das requisições HTTP, ele garante que cada chamada seja direcionada ao container correto, aplicando políticas de rate limiting, segurança e registro de logs.

Além do roteamento, o Nginx também é responsável por:

- **Rate Limiting**: Protege os microsserviços contra picos de tráfego e ataques de negação de serviço
- **Limitação de Conexões**: Controla o número de conexões simultâneas por cliente
- **Proxy Reverso**: Encaminha requisições para os containers internos na rede `app-network`
- **Headers de Proxy**: Preserva informações originais da requisição (IP do cliente, protocolo, etc.)

---

## Configuração do Nginx

```nginx
events {}

http {
    limit_req_zone $binary_remote_addr zone=api_limit:10m rate=5r/s;
    limit_conn_zone $binary_remote_addr zone=conn_limit:10m;
    
    server {
        listen 80;
        client_max_body_size 1m;
        
        # Blocos de roteamento
        location /plataforma/ { ... }
        location /cadastro-corretora-api/ { ... }
        ...
    }
}
```

O Nginx utiliza zonas de memória compartilhada para controlar o tráfego e proteger os microsserviços:

| Diretiva |  Descrição                         |
|--------|-----------------------------------|
| `limit_req_zone` | Define uma zona para rate limiting baseada no IP do cliente. Capacidade de 10MB e taxa de 5 requisições por segundo. |
| `limit_conn_zone` | Define uma zona para limitação de conexões simultâneas por IP. Capacidade de 10MB. |
