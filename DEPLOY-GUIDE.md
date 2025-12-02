# 📤 Guia Rápido: Enviar para Repositório Remoto

## Opção 1: GitHub

### 1. Criar repositório no GitHub
1. Acesse https://github.com/new
2. Crie um repositório (exemplo: `phticket`)
3. **NÃO** inicialize com README (já temos um)
4. Copie a URL do repositório

### 2. Adicionar remote e fazer push
```bash
# Adicione o repositório remoto
git remote add origin https://github.com/SEU-USUARIO/phticket.git

# Envie o código
git push -u origin master
```

### 3. Deploy na VPS
```bash
# Na sua VPS
cd /var/www
git clone https://github.com/SEU-USUARIO/phticket.git
cd phticket
npm install --production
cp .env.example .env
nano .env  # Configure suas variáveis
pm2 start server.js --name phticket
```

---

## Opção 2: GitLab

### 1. Criar repositório no GitLab
1. Acesse https://gitlab.com/projects/new
2. Crie um novo projeto
3. Copie a URL do repositório

### 2. Adicionar remote e fazer push
```bash
# Adicione o repositório remoto
git remote add origin https://gitlab.com/SEU-USUARIO/phticket.git

# Envie o código
git push -u origin master
```

---

## Opção 3: Repositório Privado na VPS

Se você não quer usar GitHub/GitLab, pode criar um repositório Git diretamente na VPS:

### 1. Na VPS, criar repositório bare
```bash
# Crie um diretório para o repositório
sudo mkdir -p /var/git/phticket.git
cd /var/git/phticket.git
sudo git init --bare
sudo chown -R $USER:$USER /var/git/phticket.git
```

### 2. No seu computador local
```bash
# Adicione a VPS como remote
git remote add vps usuario@seu-servidor.com:/var/git/phticket.git

# Envie o código
git push vps master
```

### 3. Na VPS, clone para o diretório de trabalho
```bash
cd /var/www
git clone /var/git/phticket.git
cd phticket
npm install --production
cp .env.example .env
nano .env  # Configure suas variáveis
pm2 start server.js --name phticket
```

---

## 🔄 Workflow de Atualização

### Após fazer alterações localmente:

```bash
# 1. Adicione as mudanças
git add .

# 2. Faça commit
git commit -m "descrição das alterações"

# 3. Envie para o repositório remoto
git push origin master
```

### Na VPS, atualizar o código:

```bash
cd /var/www/phticket
git pull origin master
npm install --production  # Se houver novas dependências
pm2 restart phticket
```

---

## 📋 Checklist antes do primeiro deploy

- [ ] Arquivo `.env` configurado na VPS (NÃO commitar senhas!)
- [ ] Node.js 18+ instalado na VPS
- [ ] PM2 instalado globalmente (`npm install -g pm2`)
- [ ] Portas 80 e 443 abertas no firewall
- [ ] Nginx instalado e configurado
- [ ] SSL configurado (opcional, mas recomendado)
- [ ] Dependências do Chrome instaladas na VPS

---

## 🆘 Problemas Comuns

### Erro: Permission denied (publickey)
```bash
# Adicione sua chave SSH ao servidor remoto
ssh-copy-id usuario@seu-servidor.com
```

### Erro: EADDRINUSE (porta em uso)
```bash
# Na VPS, veja qual processo está usando a porta
sudo lsof -i :5500
# Mate o processo ou use outra porta
```

### WhatsApp não conecta na VPS
```bash
# Instale as dependências do Chrome
sudo apt install -y chromium-browser chromium-codecs-ffmpeg
```

---

## 📞 Suporte

Se encontrar problemas, verifique:
1. Logs do PM2: `pm2 logs phticket`
2. Logs do Nginx: `sudo tail -f /var/log/nginx/error.log`
3. Logs do sistema: `cat logs/app.log`
