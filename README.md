# instalador-odoo
instalador odoo qualquer versão

Basta informar o domínio, a versão do Odoo, e aguardar a instalação.

```bash
wget https://raw.githubusercontent.com/tonnybarros/instalador-odoo/refs/heads/main/instaladorOdoo.sh && sudo chmod -R 777 instaladorOdoo.sh
```
Depois execute nano instaladorOdoo.sh, edite:
ODOO_VERSION="16.0"  # Ajuste para a versão desejada
DOMAIN_NAME="seudominio.com.br"  # Substitua pelo seu domínio
ADMIN_EMAIL="seuemail@gmail.com"  # Substitua pelo e-mail do administrador para SSL

Por fim, rode sh ./instalador.sh
