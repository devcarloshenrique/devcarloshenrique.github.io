---
title: "Roteirização Inteligente para Veículos de Carga com Mapbox e TomTom no React Native"
date: 2024-08-28
summary: Como resolvemos os desafios de evitar viadutos baixos e pontes com limitação de tonelagem em navegação mobile em tempo real.
categories: [Mobile & Geolocation]
tags: [React Native, Mapbox, TomTom API, GPS, Mobile, TypeScript]
readingTime: 7 min de leitura
---

## O Risco das Rotas Convencionais para Caminhões

Aplicativos tradicionais de navegação como Google Maps e Waze foram desenhados primordialmente para automóveis de passeio. Para um caminhoneiro conduzindo um veículo de 4,20m de altura e 30 toneladas, seguir uma rota de carro de passeio pode resultar em acidentes graves em túneis ou viadutos baixos, além de multas por trafegar em vias com restrição de eixos.

No desenvolvimento do **RotaTruck**, o objetivo foi criar uma experiência nativa fluida em React Native com foco nas regras de circulação de veículos de carga.

---

## Integração Híbrida: Visualização com Mapbox + Roteamento TomTom

Identificamos que a API de Roteirização Comercial da TomTom possui parâmetros especializados para:

- Altura do veículo (`vehicleHeight`)
- Largura e comprimento (`vehicleWidth`, `vehicleLength`)
- Peso total por eixo (`vehicleAxleWeight`)
- Tipo de carga perigosa (se aplicável)

No entanto, a renderização vetorial de mapas do Mapbox (`@rnmapbox/maps`) oferece melhor desempenho gráfico e customização visual no ecossistema mobile. Por isso, desacoplamos os dois serviços:

```typescript
// src/services/routingService.ts
export async function calculateTruckRoute(
  origin: Coordinates,
  destination: Coordinates,
  specs: TruckSpecs
): Promise<GeoJSON.Feature<GeoJSON.LineString>> {
  const url = `https://api.tomtom.com/routing/1/calculateRoute/${origin.lat},${origin.lng}:${destination.lat},${destination.lng}/json` +
    `?vehicleHeight=${specs.heightMeters}&vehicleWeight=${specs.weightKg}&travelMode=truck&key=${TOMTOM_KEY}`;

  const response = await fetch(url);
  const data = await response.json();

  // Converte a geometria de pontos para o formato padrão GeoJSON esperado pelo Mapbox
  return toGeoJSON(data.routes[0].legs[0].points);
}
```

A interface renderiza a rota sobre o mapa com marcadores preditivos e alertas sonoros quando o motorista se aproxima de trechos que exigem atenção redobrada.
