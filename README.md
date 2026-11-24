# Financial Domains Intelligence Database

Base mundial de domínios oficiais de empresas do setor financeiro. Gerada em 2026-11-24.

## Arquivos
| arquivo | conteúdo |
|---|---|
| `banks.csv` | bancos tradicionais, digitais, neobanks, credit unions, savings |
| `fintechs.csv` | fintechs genéricas, launchpads, VC, RWA, categorias não mapeadas |
| `payment-processors.csv` | processadores, redes de cartão, transferências ACH, EMIs |
| `payment-gateways.csv` | gateways (incl. subcategoria crypto) |
| `brokers.csv` | corretoras (ações, forex, online), prediction markets |
| `crypto-exchanges.csv` | CEX, DEX, derivativos, agregadores |
| `crypto-wallets.csv` | wallets cripto |
| `insurance.csv` | seguradoras, insurtechs, DeFi cover |
| `lending.csv` | empréstimos, p2p lending, BNPL, microfinanças, protocolos DeFi |
| `remittance.csv` | remessas internacionais |
| `crowdfunding.csv` | plataformas de investimento coletivo |
| `open-finance.csv` | participantes de open banking/open finance (BR, UK) |
| `financial-infrastructure.csv` | bridges, chains, oráculos, clearing, stock exchanges, staking, stablecoins, redes de pagamento |
| `e-wallets.csv` | carteiras digitais não-crypto (extra) |
| `fx-exchange.csv` | casas de câmbio (extra) |
| `all-domains.csv` | consolidação deduplicada por domínio |
| `sources.csv` | rastreabilidade de todas as fontes (+ falhas) |
| `statistics.json` | estatísticas de cobertura |

## Schema (9 colunas + 1 extra)
`name,domain,category,subcategory,country,source,source_url,verified,verification_date,alternative_domains`

- **domain**: eTLD+1 resolvido via Public Suffix List (ICANN). Subdomínios de API (ex.: `api.banco.com.br`) são reduzidos ao domínio registrável.
- **country**: ISO-3166 alpha-2, múltiplos países separados por `|`. Vazio = não determinado.
- **source**: fontes múltiplas separadas por `|`. Ver `sources.csv` para URL e tipo.
- **verified** (semântica):
  - `verified` — domínio confirmado por fonte oficial/regulatória (FDIC, Open Finance Brasil, OBL UK, espelho BCB) ou espelho direto de registro oficial.
  - `unverified` — encontrado em fonte comunitária/agregador (Wikidata, DefiLlama, CoinGecko, GitHub, etc.); propriedade não confirmada.
  - `inactive` — sweep HTTP de 2026-11-24 falhou (DNS não resolveu, sem resposta TLS/TCP, ou resposta de erro persistente). Não implica domínio expirado — CDNs podem bloquear o scanner.
  - `disputed` — 2+ fontes confiáveis atribuem nomes de empresas diferentes ao mesmo domínio.
- **alternative_domains**: domínios registráveis adicionais da mesma organização quando a fonte os fornece (diretórios de open banking); vazio na maioria.
- HTTP 200 **não** marca `verified` — liveness só infuencia `inactive`.

## Metodologia
1. Fontes obrigatórias + APIs estruturadas + descoberta por busca/GitHub (ver `sources.csv`).
2. Extração de nome + URL por fonte; normalização (protocolo/caminho removidos, IDNA, lowercase, eTLD+1).
3. Sintaxe validada (RFC-lite + PSL); IPs, hosts inválidos e exemplos descartados.
4. Deduplicação por domínio; categorias/países/fontes mesclados sem perda.
5. Liveness sweep HTTP concorrente (HEAD→GET, HTTPS→HTTP fallback).
6. Estatísticas em `statistics.json`.

## Limitações
- `wallet-attribution` (fonte obrigatória) não contém websites — atribuição endereço→entidade; contribuiu 0 domínios.
- Registros oficiais sem campo de site (EBA, ECB/SSM, BCB instituições, RBI etc.) não produzem domínios e estão documentados como não processáveis em `sources.csv`.
- `inactive` pode ter falsos positivos (WAF bloqueando datacenter). Re-sweep manual recomendado antes de descartar.
- Wikidata tem ruído (nomes desatualizados, sites de projetos). `unverified` sinaliza isso.
- CDR-AU (Austrália) retornou 404 no endpoint público — não incluído.
