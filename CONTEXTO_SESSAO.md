# Contexto da Sessão — Thalamus

## O que é o projeto

Dois repos GitHub (GitHub Pages, single-file HTML + Supabase JS CDN):
- `thalamus-app` → portal do cliente (login + dashboard + catálogo + minhas soluções + faturamento)
- `thalamus-admin` → painel admin (gestão de empresas, soluções, faturamento)

**Supabase URL:** `https://emothfrrdtbhkmpsffhc.supabase.co`

---

## Tarefa pendente — CRIAR TABELAS NO SUPABASE

O MCP do Supabase foi registrado nesta sessão. Use-o para rodar o SQL abaixo:

```sql
-- Tabela de soluções contratadas por empresa
CREATE TABLE company_solutions (
  id            uuid DEFAULT gen_random_uuid() PRIMARY KEY,
  company_id    uuid REFERENCES companies(id) ON DELETE CASCADE NOT NULL,
  solution_id   text NOT NULL,
  status        text NOT NULL DEFAULT 'contratado',
  contracted_at timestamptz DEFAULT now()
);

ALTER TABLE company_solutions ENABLE ROW LEVEL SECURITY;

CREATE POLICY "client reads own solutions" ON company_solutions
  FOR SELECT USING (
    company_id IN (SELECT id FROM companies WHERE user_id = auth.uid())
  );

CREATE POLICY "client inserts own solutions" ON company_solutions
  FOR INSERT WITH CHECK (
    company_id IN (SELECT id FROM companies WHERE user_id = auth.uid())
  );

CREATE POLICY "admin manages solutions" ON company_solutions
  FOR ALL USING (
    EXISTS (SELECT 1 FROM companies WHERE user_id = auth.uid() AND is_admin = true)
  );

-- Tabela de cobranças adicionais
CREATE TABLE billing_extras (
  id          uuid DEFAULT gen_random_uuid() PRIMARY KEY,
  company_id  uuid REFERENCES companies(id) ON DELETE CASCADE NOT NULL,
  description text NOT NULL,
  amount      numeric(10,2) NOT NULL DEFAULT 0,
  type        text DEFAULT 'pontual',
  created_at  timestamptz DEFAULT now()
);

ALTER TABLE billing_extras ENABLE ROW LEVEL SECURITY;

CREATE POLICY "client reads own extras" ON billing_extras
  FOR SELECT USING (
    company_id IN (SELECT id FROM companies WHERE user_id = auth.uid())
  );

CREATE POLICY "admin manages extras" ON billing_extras
  FOR ALL USING (
    EXISTS (SELECT 1 FROM companies WHERE user_id = auth.uid() AND is_admin = true)
  );
```

---

## Estado atual dos repos

### thalamus-app
- Login imersivo (Orbitron + Rajdhani, fundo atmosférico com orbs)
- Portal: Dashboard, Catálogo, Minhas Soluções, Faturamento
- Soluções: Portal de Cobrança Conexa (R$295/mês), Dashboard de Resultados (R$250/mês), Agente de IA WhatsApp (R$200/mês)
- Catálogo filtra soluções já contratadas (somem quando contratadas)
- Faturamento: resumo mensal, breakdown, previsão 3 meses

### thalamus-admin
- Login simples com logo thalamus.png
- Lista de empresas na sidebar
- Tabs por empresa: Geral / Soluções / Faturamento
- Soluções: contratar, atualizar status (contratado → em_configuracao → em_implantacao → ativo), remover
- Faturamento: cobranças adicionais (pontual/recorrente), resumo mensal

---

## Zelda (Dev Senior)
Persona ativa nesta sessão. Arquivo de memória em:
`C:\Users\aceya\Projects\EquipeSuperStar\memory\machine learning\zelda_evolucao.md`

Para continuar com a Zelda, inicie com: `zelda`
