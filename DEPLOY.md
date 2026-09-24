# Como o site vai ao ar

O repositório **é** o site. Tudo que entra na branch `main` sobe sozinho:

1. **GitHub Pages** (preview): `https://victorsntz.github.io/romerodocessite/` — publica em ~1 min a cada push. Fica no ar até a virada do domínio.
2. **Hostinger** (domínio próprio): o hPanel puxa este repositório (Git deploy) a cada push na `main`. O workflow `.github/workflows/deploy.yml` acompanha o push e confere se o site respondeu com os arquivos novos.

Fluxo do dia a dia: pedir a alteração → commit + push na `main` → no ar nos dois lugares. Não existe FTP nem edição direta no servidor.

## Ativar na Hostinger (uma vez)

No hPanel: **Websites → (site) Gerenciar/Dashboard → Avançado → Git**.

**Se aparecer "Conectar com GitHub"** (integração nova, preferida):
1. Conectar com a conta GitHub `victorsntz` (é quem tem o repositório; pode ser feito mesmo que o hPanel seja de outra pessoa) e instalar o app da Hostinger quando pedir.
2. Repositório: `victorsntz/romerodocessite` · Branch: `main` · Diretório raiz: `public_html`.
3. Antes de implantar, esvaziar a `public_html` (apagar o `default.php` que a Hostinger cria).
4. Implantar. Depois disso todo push publica sozinho; o botão **Reimplantar** força na mão.

**Se aparecer "Criar novo repositório"** (Git por URL, mais antigo):
1. Repositório: `https://github.com/victorsntz/romerodocessite.git` · Branch: `main` · Diretório: em branco (= `public_html`, precisa estar vazia).
2. Criar e clicar em **Implantar**.
3. Em **Implantação automática**, copiar a URL do webhook e guardar no GitHub como segredo (o workflow chama essa URL a cada push):
   ```bash
   gh secret set HOSTINGER_WEBHOOK -R victorsntz/romerodocessite
   ```
   (cole a URL quando pedir).

**Nos dois casos, para o workflow conferir o site no ar**, gravar a URL do domínio como variável do repositório:
```bash
gh variable set SITE_URL -R victorsntz/romerodocessite --body "https://SEU-DOMINIO.com.br"
```

**Domínio e SSL** (hPanel): o domínio comprado na Hostinger já aponta para a hospedagem; em **Segurança → SSL** confirmar o certificado (Let's Encrypt, gratuito) e ligar **Forçar HTTPS**. O `.htaccess` também redireciona para `https://` sem `www`.

Testar na mão: `gh workflow run deploy.yml -R victorsntz/romerodocessite` e depois `gh run watch -R victorsntz/romerodocessite`.

## O que o `.htaccess` faz no domínio

- `/` serve a `Home.dc.html` direto; `/doces`, `/degustacao`, `/sobre`, `/contato` e `/cardapio-2026` funcionam como apelidos das páginas.
- Página 404 própria; bloqueio de `.git`, `.github` e arquivos de desenvolvimento; cabeçalhos de segurança; cache de fotos (30 dias) e scripts (1 dia), HTML sempre revalidado.
- No GitHub Pages ele é ignorado (por isso lá o `index.html` ainda redireciona para a Home).

## Virada do domínio (pendente — fazer de uma vez, quando o domínio existir)

Trocar `canonical`/`og:url`/`twitter:image` das 6 páginas para o domínio · links internos para as URLs limpas e 301 das `*.dc.html` (e do `Home.dc.html`/`index.html`) para elas · `sitemap.xml` com o domínio e reativar a linha `Sitemap:` no `robots.txt` · remover o `noindex` (menos do `cardapio-2026.html`, que continua fora da busca) · schema LocalBusiness/Product · páginas Termos, Privacidade e Obrigado · endereço e mapa no Contato · GTM · Search Console · apontar o GitHub Pages para o domínio (ou desligar).
