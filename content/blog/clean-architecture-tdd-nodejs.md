---
title: "Arquitetura Limpa e TDD no Node.js: Desacoplando Use Cases de Frameworks e ORMs"
date: 2025-05-03
summary: Como estruturar APIs corporativas em Node.js com TypeScript aplicando Inversão de Dependência (DIP), Repository Pattern e testes unitários em memória com Vitest.
categories: [Arquitetura & Clean Code]
tags: [Node.js, TypeScript, Clean Architecture, TDD, SOLID, Vitest, Prisma]
readingTime: 9 min de leitura
---

## O Problema dos Controladores 'Fat' e Acoplamento Excessivo

Em muitos projetos Node.js, é comum encontrar controladores que realizam validação de schema, chamadas diretas ao banco de dados via ORM, envio de e-mails e regras de negócio complexas em um único arquivo de centenas de linhas.

Essa abordagem gera graves problemas:

- Testar regras de negócio exige subir um banco de dados real em Docker a cada execução de teste.
- Mudar de ORM (por exemplo, de Prisma para Drizzle ou Knex) exige reescrever a aplicação inteira.
- Mudanças nas rotas HTTP afetam diretamente a lógica central do domínio.

---

## O Princípio da Inversão de Dependência (DIP)

No projeto **nodejs-api-check-in**, adotei a arquitetura limpa em camadas com separação estrita de responsabilidades:

```
src/
├── domain/            # Entidades puras e regras invariantes de negócio
├── use-cases/         # Casos de uso da aplicação (Regras de Negócio)
├── repositories/      # Interfaces de contratos (Ports)
├── infra/
│   ├── database/      # Implementações reais com Prisma / PostgreSQL
│   └── http/          # Rotas Fastify, Controllers e Middlewares
```

A regra de ouro é: **as camadas internas nunca dependem das camadas externas**. O caso de uso desconhece se os dados vêm do PostgreSQL, de um cache Redis ou de um array em memória.

---

## O Repository Pattern e Testes em Menos de 100ms

Definimos uma interface simples para o repositório de check-ins:

```typescript
// src/repositories/check-ins-repository.ts
export interface CheckInsRepository {
  create(data: Prisma.CheckInUncheckedCreateInput): Promise<CheckIn>;
  findByUserIdOnDate(userId: string, date: Date): Promise<CheckIn | null>;
  countByUserId(userId: string): Promise<number>;
}
```

Para os testes unitários, criamos um **InMemoryCheckInsRepository**:

```typescript
// src/repositories/in-memory/in-memory-check-ins-repository.ts
export class InMemoryCheckInsRepository implements CheckInsRepository {
  public items: CheckIn[] = [];

  async findByUserIdOnDate(userId: string, date: Date) {
    const startOfTheDay = dayjs(date).startOf('date');
    const endOfTheDay = dayjs(date).endOf('date');

    const checkInOnSameDate = this.items.find((checkIn) => {
      const checkInDate = dayjs(checkIn.created_at);
      const isOnSameDate = checkInDate.isAfter(startOfTheDay) && checkInDate.isBefore(endOfTheDay);
      return checkIn.user_id === userId && isOnSameDate;
    });

    return checkInOnSameDate ?? null;
  }
}
```

### O Resultado Prático

Com essa abordagem, executamos mais de **80 casos de teste unitários em menos de 300 milissegundos com Vitest**. Não há necessidade de subir containers Docker para validar que um usuário não pode fazer dois check-ins no mesmo dia ou validar a fórmula de Haversine para distâncias geodésicas superiores a 100 metros.

---

## Validação Geodésica de Proximidade (Fórmula de Haversine)

Uma das regras críticas do sistema é garantir que o aluno só possa realizar check-in se estiver fisicamente na academia. Em vez de delegar cálculos pesados de GIS para o banco de dados em todas as consultas, encapsulamos a validação matemática no caso de uso:

```typescript
// src/utils/get-distance-between-coordinates.ts
export function getDistanceBetweenCoordinates(
  from: Coordinate,
  to: Coordinate,
): number {
  if (from.latitude === to.latitude && from.longitude === to.longitude) {
    return 0;
  }

  const fromRadian = (Math.PI * from.latitude) / 180;
  const toRadian = (Math.PI * to.latitude) / 180;
  const theta = from.longitude - to.longitude;
  const radTheta = (Math.PI * theta) / 180;

  let dist =
    Math.sin(fromRadian) * Math.sin(toRadian) +
    Math.cos(fromRadian) * Math.cos(toRadian) * Math.cos(radTheta);

  dist = Math.min(dist, 1);
  dist = Math.acos(dist);
  dist = (dist * 180) / Math.PI;
  dist = dist * 60 * 1.1515;
  dist = dist * 1.609344; // Retorno em quilômetros

  return dist;
}
```

### Conclusão

Investir em Clean Architecture e TDD não atrasa o projeto; ao contrário, liberta o desenvolvedor do medo de refatorar código e proporciona uma base estável pronta para escalar.
