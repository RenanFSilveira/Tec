# LP Monitoramento — TEC Segurança Eletrônica

Landing page de captação de leads da TEC Segurança Eletrônica (Grupo Fibra), destino do tráfego
de Google Ads (rede de pesquisa). Converte busca de alta intenção em pedido de orçamento, com
qualificação antes do contato.

**Arquivo único:** `index.html`. Sem build, sem dependência externa além da fonte do Google Fonts.
Abra o arquivo no navegador para ver a página.

---

## Configuração antes de publicar

Todo o que precisa ser preenchido está no bloco `CFG`, no início do `<script>` no fim do arquivo.

| Campo | O que é |
| :--- | :--- |
| `whatsapp` | Número de destino, só dígitos com DDI. **Único campo obrigatório.** |
| `webhook` | Endpoint que recebe o lead (n8n ou webhook do CRM). Vazio = não envia. |
| `ga4` | `G-XXXXXXXXXX` |
| `googleAds` | `AW-XXXXXXXXXX` |
| `convLead` | `AW-XXXXXXXXXX/rótulo` — conversão **primária** (envio do formulário) |
| `convZap` | `AW-XXXXXXXXXX/rótulo` — conversão **secundária** (clique no WhatsApp) |
| `metaPixel` | ID do pixel da Meta |
| `gtm` | `GTM-XXXXXXX`. Se preenchido, **desliga as tags diretas sozinho.** |

Campo vazio não carrega nada e não quebra a página.

**Tag direta e GTM são excludentes por construção.** O modo padrão é tag direta (`gtag.js` + `fbq`).
Preencher `gtm` desliga `ga4`, `googleAds` e `metaPixel` automaticamente, para nunca contar conversão
duas vezes. O `dataLayer` é alimentado nos dois modos, então migrar depois não exige reescrever nada.

Com `whatsapp` errado o lead vai para o número errado e ninguém percebe. É o primeiro item a conferir.

---

## Eventos

Disparados no `dataLayer` nos dois modos de rastreamento:

| Evento | Quando |
| :--- | :--- |
| `lp_view` | carregamento da página |
| `cta_click` | clique em botão que leva ao formulário |
| `form_etapa` | avanço de passo do formulário |
| `faq_open` | abertura de uma pergunta do FAQ |
| `whatsapp_click` | clique em qualquer botão de WhatsApp |
| `lead_form_submit` | **envio do formulário** — é a conversão primária |

Todo CTA carrega um `origem_botao` próprio (`topo`, `heroi`, `migracao`, `final`, `barra_fixa`,
`resultado`, entre outros), o que permite ver qual bloco da página converte.

O `telefone_e164` vai no `dataLayer` pronto para alimentar conversões avançadas do Google Ads.

---

## Publicação

Hospedagem estática, HTTPS obrigatório. Qualquer host serve (Vercel, Netlify, Cloudflare Pages,
GitHub Pages) porque é arquivo único sem build.

O `<link rel="canonical">` no `<head>` aponta para `https://monitoramento.tecseguranca.com/`.
**Se publicar em outro endereço, atualizar o canonical**, senão ficam duas URLs indexáveis do
mesmo conteúdo.

---

## Ao editar o `index.html`

Três coisas não podem ser removidas sem quebrar a página em produção:

1. **O `window.open` tem que ser a primeira instrução do envio do formulário.** Fora do gesto do
   usuário, o bloqueador de pop-up do navegador mobile cancela a abertura do WhatsApp.
2. **A classe `js` no `<html>` e a rede de segurança de 2,6 s da animação de entrada.** Sem elas,
   uma falha de script deixa seções inteiras invisíveis.
3. **O `push()` como único ponto de saída de evento.** Disparar `gtag` ou `fbq` direto em outro
   lugar do arquivo é o caminho mais curto para contagem dobrada.

---

## Pendências marcadas no arquivo

Procure por `[CONFIRMAR]` no HTML:

- número de WhatsApp de destino (o atual veio do site institucional)
- arquivo de logo oficial, hoje substituído por um wordmark em SVG
- ID do pixel da Meta
- FAQ: raio de atendimento da unidade volante e prazo médio de instalação
- rodapé: telefones e endereço, conferir se seguem atuais
- placeholders visuais (painel da central no topo) a trocar por foto real
