# ROLLBACK.md — Procedimentos de Reversão

> Guia de emergência para reverter mudanças no sistema Vortex/OpenClaw.

## Como Reverter uma Mudança

### 1. Backup Automático
O sistema cria backups automáticos diariamente em `backups/YYYY-MM-DD/openclaw.json`.

**Para restaurar um backup específico:**
```bash
# Parar o Gateway
sudo systemctl stop openclaw

# Copiar o backup
cp backups/2026-03-24/openclaw.json /root/.openclaw/openclaw.json

# Iniciar o Gateway
sudo systemctl start openclaw
```

### 2. Reversão Manual (Último Recurso)
Se o backup automático falhar ou o sistema não iniciar:

1.  Acesse o servidor via SSH.
2.  Navegue até `/root/.openclaw/`.
3.  Edite o `openclaw.json` manualmente para remover a última mudança feita.
    *   Exemplos de mudanças comuns: `agents.defaults.model`, `cron` jobs adicionados, `channels` modificados.
4.  Reinicie o Gateway: `sudo systemctl restart openclaw`.

### 3. Comandos Úteis
- Ver config atual: `openclaw gateway config.get`
- Ver jobs de cron: `openclaw cron list`
- Deletar job de cron: `openclaw cron remove <job-id>`