# Laboratório GNS3 — Provedor do Zero (PPPoE)

Este laboratório demonstra, passo a passo, como construir um **provedor de internet do zero** usando **GNS3 + MikroTik**, simulando:

- Uplink com provedor (PPPoE Client)
- Rede de gerência com acesso à internet
- Servidor PPPoE para clientes
- Autenticação de clientes (ppp-secrets)
- NAT/CGNAT
- Estrutura base para evolução (MK-AUTH, BGP, etc.)

---

## 1. Objetivo do Laboratório

Criar um cenário funcional onde:

- O roteador **ISP-Padrao2** atua como o provedor
- Recebe internet via **PPPoE Client** (PROVEDOR-Y)
- Entrega internet para:
  - Rede de **Gerência**
  - **Clientes PPPoE** (Luca, Débora, Isabela)

---

## 2. Topologia (Visão Lógica)

Componentes principais:

- **NAT1 (Cloud)**: Simula a internet
- **PROVEDOR-Y**: Provedor upstream
- **ISP-Padrao2 (MikroTik)**: Roteador principal do provedor
- **OLT-SW-ACCESS**: Switch de acesso
- **Roteadores Clientes**: Luca, Débora, Isabela
- **PC Gerência**: Máquina administrativa
- **MK-AUTH**: Autenticação (backlog)

---

## 3. Plano de Endereçamento (Exemplo)

### 3.1 Uplink (ISP → Provedor)

- Tipo: PPPoE Client
- Interface: WAN (ex: ether2)
- IP: Atribuído pelo provedor
- DNS: Via PPP ou manual

### 3.2 Rede de Gerência

| Item | Valor |
|---|---|
| Rede | 192.168.0.0/24 |
| Gateway | 192.168.0.1 |
| PC Gerência | DHCP ou 192.168.0.2 |

### 3.3 Pool de Clientes (CGNAT)

| Item | Valor |
|---|---|
| Pool Clientes | 100.64.1.0/24 |
| Local Address PPP | 10.64.1.1 |
| DNS Clientes | 8.8.8.8 |

---

## 4. Configuração do ISP-Padrao2 (MikroTik)

### Etapa A — PPPoE Client (Uplink)

1. Criar PPPoE Client na interface WAN
2. Informar usuário e senha do provedor
3. Habilitar:
   - `Add Default Route`
   - `Use Peer DNS`

**Validação**
- `ping 8.8.8.8`
- `ping google.com`
- Verificar rota default

---

### Etapa B — Rede de Gerência + NAT

1. Atribuir IP à interface de gerência:
2. Criar servidor DHCP (opcional)
3. Criar regra NAT:
- `src-address=192.168.0.0/24`
- `out-interface=PPPoE-WAN`
- `action=masquerade`

**Validação**
- PC gerência navega na internet
- DNS resolve nomes

---

### Etapa C — Servidor PPPoE (Clientes)

1. Criar Pool de IPs:


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
