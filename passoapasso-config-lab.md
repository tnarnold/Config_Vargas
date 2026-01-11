
2. Criar PPP Profile:
- Local Address: `10.64.1.1`
- Remote Address: Pool Clientes
- DNS: `8.8.8.8`

3. Criar PPPoE Server:
- Interface: ACESSO (ligada ao switch)
- Profile: Criado acima

4. Criar NAT para clientes:
- `src-address=100.64.1.0/24`
- `out-interface=PPPoE-WAN`
- `action=masquerade`

---

### Etapa D — Criar Clientes PPPoE

Criar os usuários (PPP Secrets):

| Cliente | Usuário |
|---|---|
| Luca | luca |
| Débora | debora |
| Isabela | isabela |

Parâmetros:
- Service: `pppoe`
- Profile: Perfil PPP criado
- Password: Definir

**Validação**
- PPP → Active Connections
- Cliente recebe IP
- Cliente navega

---

## 5. Troubleshooting Comum

### Gerência não navega
- IP e gateway corretos
- DNS configurado
- NAT ativo
- Firewall não bloqueando forward

### PPPoE cliente conecta/desconecta
- Interface errada no PPPoE Server
- Usuário inexistente
- Pool esgotado
- Loop no switch

### Tráfego anormal em cliente
- Verificar Torch
- Conferir loops L2
- Downloads automáticos ativos

---

## 6. Checklist de Conclusão

- [ ] PPPoE Client conectado
- [ ] Rota default ativa
- [ ] Gerência com acesso à internet
- [ ] PPPoE Server ativo
- [ ] Clientes autenticando
- [ ] Clientes navegando
- [ ] Configuração exportada

---

## 7. Próximos Passos (Backlog)

- Integração com MK-AUTH (RADIUS)
- Planos de velocidade (Queues)
- CGNAT avançado
- Firewall por perfil
- BGP com provedor
- Monitoramento (Zabbix)

---

## 8. Observações

Este laboratório é **didático** e tem como objetivo ensinar os conceitos fundamentais de um provedor real.

Não deve ser usado diretamente em produção sem ajustes de segurança e capacidade.
