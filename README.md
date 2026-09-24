# Mecânica Mantovani — Site otimizado

## Resultado no Google Lighthouse (mobile)

| Categoria       | Antes  | Depois |
|-----------------|--------|--------|
| Performance     | 52/100 | **96/100** |
| Accessibility   | —      | **100/100** |
| Best Practices  | —      | **100/100** |
| SEO             | —      | **100/100** |

Print completo em `print-lighthouse.png` (gerado com o Lighthouse CLI,
mesma engine usada pela aba Lighthouse do Chrome DevTools, contra o
projeto rodando em servidor local).

## Principais problemas encontrados e correções

Todos os erros estão comentados diretamente no código (`index.html` e
`style.css`) no formato ERRO / SOLUÇÃO / MOTIVO. Resumo:

1. **Sem `<meta name="viewport">`** — o navegador mobile renderizava a
   página como desktop e depois dava zoom out. Adicionada a meta tag.
2. **Larguras fixas em pixels** (`body { width: 1280px }`,
   `.hero-image { width: 1100px }`, `.service-card { width: 280px }`) —
   estouravam a tela em qualquer viewport menor, causando rolagem
   horizontal. Trocadas por `max-width`, `%`, `clamp()` e CSS Grid
   (`auto-fit`/`minmax`), que se adaptam a qualquer tamanho de tela.
3. **Imagens gigantes** — as 5 fotos originais em PNG somavam ~14,9 MB
   (até 7728×5152px) para serem exibidas em miniaturas de poucas
   centenas de pixels. Foram redimensionadas para o tamanho real de
   exibição e convertidas para WebP (com fallback em JPEG via
   `<picture>`), somando ~363 KB no total — mais de 97% de redução.
   Isso foi o principal responsável pela nota baixa de Performance.
4. **Sem `width`/`height` nas imagens** — causava Cumulative Layout
   Shift (o conteúdo "pulava" enquanto a imagem carregava). Adicionados
   os atributos proporcionais ao tamanho real.
5. **Imagem do hero sem prioridade de carregamento** — adicionado
   `<link rel="preload">` + `fetchpriority="high"` para acelerar o LCP
   (Largest Contentful Paint); imagens dos cards (fora da primeira
   dobra) usam `loading="lazy"`.
6. **Falta de `alt` nas imagens** — prejudicava acessibilidade e SEO.
   Todas as imagens receberam texto alternativo descritivo.
7. **Contraste de cor insuficiente** nos botões azul e verde
   (WhatsApp) — ajustados para tons mais escuros que atingem a razão
   mínima de contraste 4.5:1 exigida pelo WCAG/Lighthouse.
8. **Sem meta description** — prejudicava SEO. Adicionada.
9. **Botões "Agendar" sem nenhuma ação** — viraram links reais para o
   WhatsApp com mensagem pronta por serviço.
10. **Favicon ausente** gerava erro 404 no console (reprovava o audit
    "no errors in console"). Adicionado favicon SVG embutido.

## Desafio extra atendido

- **Seções "Sobre Nós" e "Contato"** criadas (os links do menu já
  apontavam para `#sobre` e `#contato`, mas as seções não existiam).
- **Integração com WhatsApp** — botão de contato e cada "Agendar" dos
  serviços abrem uma conversa no WhatsApp com mensagem pré-preenchida
  (número fictício `(45) 99999-9999`, ajuste para o número real do
  cliente).
- **Google Maps** — mapa incorporado na seção Contato (`iframe`
  responsivo, `loading="lazy"`) apontando para Toledo/PR.

## Como rodar

1. Extraia esta pasta.
2. Abra no VS Code e ative a extensão **Live Server**.
3. Clique em "Go Live" e rode o Lighthouse pelo DevTools (F12 →
   aba Lighthouse) normalmente — o mapa do Google Maps precisa de
   internet para carregar.
