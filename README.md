# ✈️ BC Travel — Gestão de Agência de Viagens no Salesforce

Projeto prático de **modelagem de dados no Salesforce** (Lightning Experience), baseado no case **TravelCorp** do curso **Corporação Salesforce** e adaptado para a minha agência fictícia, a **BC Travel**.

O objetivo é praticar, em uma Developer Edition, o ciclo completo de construção de um app declarativo (*clicks, not code*): objetos personalizados, campos, relacionamentos, fórmulas, roll-up summary e validação com dados de teste.

![Status](https://img.shields.io/badge/status-em%20andamento-yellow)
![Plataforma](https://img.shields.io/badge/plataforma-Salesforce-00A1E0)
![Tipo](https://img.shields.io/badge/tipo-low--code-blue)

---

## 📌 Sumário

- [Contexto e objetivos](#-contexto-e-objetivos)
- [Modelo de dados](#-modelo-de-dados)
- [Objetos e campos](#-objetos-e-campos)
- [Relacionamentos](#-relacionamentos)
- [Fórmulas e totalizações](#-fórmulas-e-totalizações)
- [Como reproduzir](#-como-reproduzir)
- [Testes realizados](#-testes-realizados)
- [Evidências (prints)](#-evidências-prints)
- [Aprendizados](#-aprendizados)
- [Próximos passos](#-próximos-passos)
- [Autor](#-autor)

---

## 🎯 Contexto e objetivos

Este projeto faz parte do meu estudo de Salesforce e tem como objetivos:

- Fixar o aprendizado com um ambiente próximo do dia a dia da profissão
- Treinar a resolução de problemas de modelagem de dados
- Estruturar um portfólio público com evidências do que construí

A estrutura é guiada por *cards* (user stories com critérios de aceitação), no estilo de um backlog real.

---

## 🗂️ Modelo de dados

```text
 Destino ──(Master-Detail)──< Pacote de Viagem >──(Master-Detail)── Reserva ──(Master-Detail)──> Cliente
                                    │                                                              │
                                    └──< Pacote-Atividade >── Atividade Turística                  (Lookup)
                                          (junção N:N)                                                 │
                                                                                                    Usuário
```

> Diagrama gerado também pelo **Schema Builder** do Salesforce (veja a seção de evidências).

---

## 🧱 Objetos e campos

### Cliente
| Campo | Tipo |
|---|---|
| Nome Completo | Texto (nome do registro) |
| CPF | Texto |
| E-mail | E-mail |
| Data de Nascimento | Data |
| Cliente VIP? | Caixa de seleção |
| Idade | Fórmula (Número) |

### Destino
| Campo | Tipo |
|---|---|
| Nome do Destino | Texto (nome do registro) |
| País | Lista de opções |
| Continente | Lista de opções |
| Pontos Turísticos | Área de texto longa |
| Ativo? | Caixa de seleção |

### Pacote de Viagem
| Campo | Tipo |
|---|---|
| Nome do Pacote | Texto (nome do registro) |
| Preço Base | Moeda |
| Duração | Número |
| Nível de Luxo | Lista de opções |
| Preço Total | Fórmula (Moeda) |
| Número de Reservas | Resumo de totalização (COUNT) |

### Reserva
| Campo | Tipo |
|---|---|
| Nome da Reserva | Numeração automática (`RES-{0000}`) |
| Data da Reserva | Data/Hora |
| Status da Reserva | Lista de opções |
| Valor Pago | Moeda |
| Quantidade de Membros | Número |

### Atividade Turística
| Campo | Tipo |
|---|---|
| Nome da Atividade | Texto (nome do registro) |
| Duração Estimada | Número |
| Custo Adicional | Moeda |

### Pacote-Atividade (objeto de junção)
| Campo | Tipo |
|---|---|
| Pacote de Viagem | Relacionamento de pesquisa (Lookup) |
| Atividade Turística | Relacionamento de pesquisa (Lookup) |
| Ordem de Exibição | Número |
| Inclusa por padrão? | Caixa de seleção |

---

## 🔗 Relacionamentos

| Objeto filho | Objeto pai | Tipo |
|---|---|---|
| Pacote de Viagem | Destino | Master-Detail |
| Reserva | Cliente | Master-Detail |
| Reserva | Pacote de Viagem | Master-Detail |
| Cliente | Usuário | Lookup |
| Pacote-Atividade | Pacote de Viagem | Lookup |
| Pacote-Atividade | Atividade Turística | Lookup |

O objeto **Pacote-Atividade** resolve a relação **muitos-para-muitos** entre pacotes e atividades: um pacote pode ter várias atividades e uma atividade pode estar em vários pacotes.

---

## 🧮 Fórmulas e totalizações

**Idade do cliente**

```text
FLOOR((TODAY() - Data_de_Nascimento__c) / 365.25)
```

**Número de Reservas** (Resumo de totalização no Pacote de Viagem)

- Objeto resumido: Reservas
- Tipo: `COUNT`
- Depende do Master-Detail entre Reserva e Pacote de Viagem

**Preço Total** (Pacote de Viagem)

<!-- Ajuste esta seção para a fórmula que você de fato usou -->
```text
Preco_Base__c * CASE(TEXT(Nivel_de_Luxo__c),
    "Econômico", 1,
    "Conforto", 1.2,
    "Luxo", 1.5,
    1)
```

---

## 🛠️ Como reproduzir

1. Crie uma **Developer Edition** gratuita em [developer.salesforce.com/signup](https://developer.salesforce.com/signup).
2. Crie o app **BC Travel** em *Configuração > Gerenciador de Aplicativos > Novo App Lightning*.
3. Crie os 6 objetos personalizados em *Gerenciador de Objetos > Criar*, marcando a opção de criar a **guia (aba)** do objeto e incluindo-a no app.
4. Crie os campos de cada objeto (seções acima).
5. Crie os **relacionamentos** antes do Resumo de totalização, já que o roll-up depende do Master-Detail. Crie-os com as tabelas ainda vazias.
6. Crie a fórmula de Preço Total e o Resumo de totalização no Pacote de Viagem.
7. Insira registros de teste e valide (próxima seção).

> Dica: pelo App Launcher, confirme que todos os objetos aparecem no app BC Travel.

---

## ✅ Testes realizados

Roteiro de validação do modelo:

- [ ] Criar um registro de **Destino**
- [ ] Criar uma **Atividade Turística**
- [ ] Criar um **Pacote de Viagem** vinculado ao destino
- [ ] Criar um **Cliente** e conferir o cálculo da **Idade**
- [ ] Criar **duas Reservas no mesmo pacote** e conferir que o **Número de Reservas = 2**
- [ ] Conferir a numeração automática das reservas (`RES-0001`, `RES-0002`)
- [ ] Vincular a atividade ao pacote via **Pacote-Atividade**
- [ ] Verificar as listas relacionadas em Destino, Pacote e Cliente

---

## 📸 Evidências (prints)

Salve os prints na pasta `docs/images/` e referencie aqui:

| Evidência | Imagem |
|---|---|
| Objetos no Gerenciador de Objetos | `![Objetos](docs/images/01-objetos.png)` |
| Campos do objeto Cliente | `![Campos Cliente](docs/images/02-campos-cliente.png)` |
| Schema Builder (modelo de dados) | `![Schema Builder](docs/images/03-schema-builder.png)` |
| App BC Travel com a navegação | `![App](docs/images/04-app-bc-travel.png)` |
| Pacote com Número de Reservas = 2 | `![Roll-up](docs/images/05-rollup-reservas.png)` |

---

## 📚 Aprendizados

- Diferença entre **Lookup** e **Master-Detail** e quando cada um é necessário
- O **Resumo de totalização** só existe quando há Master-Detail com o objeto pai
- Um objeto só aparece na navegação do app se tiver **guia (aba)** criada
- Um Master-Detail só pode ser criado em objeto filho **sem registros**
- Uso de **objeto de junção** para relações muitos-para-muitos
- Fórmulas e numeração automática como forma de reduzir erro de digitação

---

## 🚀 Próximos passos

- [ ] Criar **Validation Rules** (ex.: Valor Pago não pode ser negativo)
- [ ] Criar **Flows** (ex.: atualizar o status da reserva automaticamente)
- [ ] Montar **Páginas Lightning** e layouts personalizados
- [ ] Criar **relatórios e dashboards** (reservas por destino, receita por pacote)
- [ ] Configurar **perfis e conjuntos de permissões**
- [ ] Estudar a evolução do projeto com Apex e LWC

---

## 👤 Autor

**José Carlos Almeida**

- GitHub: [github.com/jcalmeida-ds](https://github.com/jcalmeida-ds)
- LinkedIn: [linkedin.com/in/jcalmeida-datascience](https://linkedin.com/in/jcalmeida-datascience)

---

*Projeto educacional baseado no case TravelCorp, adaptado para a BC Travel.*
