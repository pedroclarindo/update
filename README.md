# PHTicket v1.0.0

Sistema de atendimento via WhatsApp Web com painel de gerenciamento completo.

## 📋 Requisitos

- Node.js 18+ ou superior
- Banco de dados SQLite (criado automaticamente)
- Google Chrome (opcional - whatsapp-web.js usa Chromium embutido)

## 🚀 Instalação Local

1. **Clone o repositório**
```bash
git clone <seu-repositorio>
cd PHTicket
```

2. **Instale as dependências**
```bash
npm install
```

3. **Configure as variáveis de ambiente**
```bash
# Copie o arquivo .env.example para .env
cp .env.example .env

# Edite o arquivo .env com suas credenciais
```

4. **Inicie o servidor**
```bash
npm start
```

Acesse: **http://localhost:5500**

## 🌐 Deploy na VPS

### 1. Preparar o servidor VPS

```bash
# Conecte-se à sua VPS via SSH
ssh usuario@seu-servidor.com

# Atualize o sistema
sudo apt update && sudo apt upgrade -y

# Instale Node.js (v18+)
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install -y nodejs

# Instale o PM2 para gerenciar o processo
sudo npm install -g pm2

# Instale dependências do Chrome (para whatsapp-web.js)
sudo apt install -y gconf-service libgbm-dev libasound2 libatk1.0-0 libc6 libcairo2 libcups2 libdbus-1-3 libexpat1 libfontconfig1 libgcc1 libgconf-2-4 libgdk-pixbuf2.0-0 libglib2.0-0 libgtk-3-0 libnspr4 libpango-1.0-0 libpangocairo-1.0-0 libstdc++6 libx11-6 libx11-xcb1 libxcb1 libxcomposite1 libxcursor1 libxdamage1 libxext6 libxfixes3 libxi6 libxrandr2 libxrender1 libxss1 libxtst6 ca-certificates fonts-liberation libappindicator1 libnss3 lsb-release xdg-utils wget
```

### 2. Fazer upload do código

**Opção A: Via Git (Recomendado)**
```bash
# Na VPS, clone o repositório
cd /var/www  # ou outro diretório de sua preferência
git clone <seu-repositorio> phticket
cd phticket
```

**Opção B: Via SCP/SFTP**
```bash
# No seu computador local
scp -r ./PHTicket usuario@seu-servidor.com:/var/www/phticket
```

### 3. Configurar na VPS

```bash
# Entre no diretório do projeto
cd /var/www/phticket

# Instale as dependências
npm install --production

# Configure o arquivo .env
nano .env
```

**Edite o .env na VPS:**
```env
EMAIL_SERVICE=gmail
EMAIL_USER=seu-email@gmail.com
EMAIL_PASS=sua-senha-de-app
EMAIL_FROM="PHTicket <seu-email@gmail.com>"
PORT=5500
TZ=America/Sao_Paulo
```

### 4. Iniciar com PM2

```bash
# Inicie o aplicativo
pm2 start server.js --name phticket

# Configure para iniciar automaticamente no boot
pm2 startup
pm2 save

# Verificar status
pm2 status
pm2 logs phticket
```

### 5. Configurar Nginx (Proxy Reverso)

```bash
# Instale o Nginx
sudo apt install -y nginx

# Crie o arquivo de configuração
sudo nano /etc/nginx/sites-available/phticket
```

**Conteúdo do arquivo:**
```nginx
server {
    listen 80;
    server_name seu-dominio.com;

    location / {
        proxy_pass http://localhost:5500;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

```bash
# Ative o site
sudo ln -s /etc/nginx/sites-available/phticket /etc/nginx/sites-enabled/

# Teste a configuração
sudo nginx -t

# Reinicie o Nginx
sudo systemctl restart nginx
```

### 6. Configurar SSL (HTTPS) com Let's Encrypt

```bash
# Instale o Certbot
sudo apt install -y certbot python3-certbot-nginx

# Obtenha o certificado SSL
sudo certbot --nginx -d seu-dominio.com

# Renovação automática (já configurada automaticamente)
```

## 📦 Estrutura do Projeto

```
PHTicket/
├── server.js              # Servidor Express + Socket.io + WhatsApp
├── dashboard.html         # Painel de administração
├── dashboard.js           # Lógica do painel
├── index.html             # Página de login
├── script.js              # Lógica de autenticação
├── style.css              # Estilos globais
├── package.json           # Dependências
├── .env                   # Configurações (não versionado)
├── .env.example           # Exemplo de configuração
├── database.db            # Banco SQLite (criado automaticamente)
├── logs/                  # Logs do sistema
├── uploads/               # Arquivos enviados
├── scripts/               # Scripts utilitários
└── assets/                # Imagens e recursos
```

## 🔧 Comandos Úteis

### Desenvolvimento
```bash
npm run dev        # Inicia com nodemon (reinicia automaticamente)
npm start          # Inicia em modo produção
```

### PM2 (na VPS)
```bash
pm2 start server.js --name phticket   # Iniciar
pm2 restart phticket                  # Reiniciar
pm2 stop phticket                     # Parar
pm2 delete phticket                   # Remover
pm2 logs phticket                     # Ver logs
pm2 monit                             # Monitorar recursos
```

### Atualizar código na VPS
```bash
cd /var/www/phticket
git pull origin main
npm install --production
pm2 restart phticket
```

## 🔐 Segurança

- **Nunca** commite o arquivo `.env` com senhas reais
- Use senhas fortes para contas de administrador
- Mantenha o Node.js e dependências atualizados
- Configure firewall na VPS (UFW)
```bash
sudo ufw allow 22    # SSH
sudo ufw allow 80    # HTTP
sudo ufw allow 443   # HTTPS
sudo ufw enable
```

## 📝 Observações

- As tabelas do banco são criadas automaticamente na primeira execução
- Para conectar o WhatsApp, acesse "Conexões" no painel e escaneie o QR Code
- Os logs ficam salvos em `logs/app.log`
- Para suporte, abra uma issue no repositório

## 📄 Licença

ISC
