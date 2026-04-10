# [Project Name] — Project Context

> Technical context for Orion, Nestor and Olga.
> Business vision and ideas live in `projects/<project>-ideas.md`.
> Project-specific decisions live in `projects/<project>-decisions.md`.

---

## WHAT IS [PROJECT NAME]

[One paragraph: what it does, who it's for, what makes it different]

**The right pitch:** "[One-line pitch for demos and conversations]"

### Business model
```
Free layer:
  └ [What users get for free]

Paid layer:
  └ [What users pay for]
```

---

## STACK

### Backend — `Mjosuex85/<project>-api`
```
Runtime:    
ORM:        
Auth:       
Deploy:     
DB prod:    
DB local:   
```

### Frontend — `Mjosuex85/<project>`
```
Framework:  
Styles:     
State:      
Deploy:     
```

---

## MODULES IMPLEMENTED

### [Module 1]
- [Key details]

### [Module 2]
- [Key details]

---

## BUSINESS RULES

### [Rule category]
```
[Specific rules, limits, constraints]
```

---

## INFRASTRUCTURE

```
PRODUCTION:
  Frontend  →  
  Backend   →  
  Database  →  

STAGING:
  Frontend  →  
  Backend   →  
  Database  →  

LOCAL:
  Backend   →  
  Frontend  →  
  Database  →  
```

### Deploy rules
```
develop  →  NO deploy
staging  →  Preview deploy (automatic on push)
main     →  Production deploy (automatic on PR merge by Mario)
```

---

## CI / QUALITY

### CI pipeline
- Lint + Build + Tests + Audit + SonarCloud

### Bruno QA
- `bruno.yml` in both repos — triggers on PR to staging/main

---

## TESTING STATUS

### Backend
```
[Service] → [coverage]% (status)
```

### Frontend
```
[Area] → [coverage]% (status)
```

---

## STATUS — [Date] (Session [N])

**Open / Active:**
- [issues]

**Next priorities:**
1. [priority]

---

## TARGET USERS

**Demo 1 — [Name]:** [Who they are, what they do today, what we want from them]

---

*Part of Orion OS v1.4.0 — updated [date] (Session [N])*
*Ideas and product roadmap → `projects/<project>-ideas.md`*
