flowchart TD
  %% ---------- INICIO ----------
  I0([INICIO]) --> P["Parámetros iniciales<br>grosorHilo · nColores · nMin · nMax<br>tiempoHilo · tiempoEspera · paletaColores · colorFondo<br>cantHilosUrdimbre = anchoPantalla / grosorHilo, redondeado hacia arriba<br>cantHilosTrama = altoPantalla / grosorHilo, redondeado hacia arriba"]
  P --> I1["Pintar colorFondo en todo el canvas<br>una única vez"]
  subgraph IP [" "]
    IP0(["InicializarPatron()"]) --> IP1["Elegir nColores al azar de paletaColores<br>colorAcento = posición 0<br>coloresBase = posición 1 en adelante"]
    IP1 --> IP2["n al azar entre nMin y nMax<br>k al azar entre 2 y n - 1<br>a al azar entre 1 y k - 1"]
    IP2 --> IP3["secuenciaInicial = SecuenciaEuclides(k, n)<br>en el paso i va 1 si (i * k) % n < k"]
    IP3 --> IP4["secuencia = AgregarAcento(secuenciaInicial, k, a)<br>se reparte E(a, k) sobre los k unos<br>los unos marcados pasan a 2"]

    subgraph CS [" "]
      C0(["CalcularSecuenciaColores(cantTotalPasos)"]) --> C1["indice = 0 · indiceColorBase = 0"]
      C1 --> C2{"¿indice < cantTotalPasos?"}
      C2 -->|sí| C3{"¿secuencia[indice % n]?"}
      C3 -->|1| C4["Siguiente color base<br>indiceColorBase = (indiceColorBase + 1) % cantidad de coloresBase<br>colorActual = coloresBase[indiceColorBase]"]
      C3 -->|2| C5["colorActual = colorAcento"]
      C3 -->|0| C6
      C4 --> C6
      C5 --> C6
      C6["secuenciaColores[indice] = colorActual<br>indice + 1"] --> C2
      C2 -->|no| C7(["Devuelve secuenciaColores"])
    end

    IP6["fase = 'urdimbre'<br>hiloActual = 0<br>t0 = tiempo actual"]

    IP4 -->|"para urdimbre<br>cantTotalPasos = cantHilosUrdimbre"| C0
    IP4 -->|"para trama<br>cantTotalPasos = cantHilosTrama"| C0
    C7 -->|"urdimbre = secuenciaColores"| IP6
    C7 -->|"trama = secuenciaColores"| IP6
  end

  I1 --> IP0
  IP6 --> B0

  %% ---------- BUCLE ----------
  B0([BUCLE · cada frame]) --> B1["ahora = tiempo actual"]
  B1 --> F{"¿fase?"}

  %% fase = urdimbre
  F -->|urdimbre| U1["porcentajeHiloDibujado =<br>min((ahora - t0) / tiempoHilo, 1)"]
  U1 --> U2["Dibujar urdimbre hiloActual<br>rectángulo de grosorHilo de ancho<br>y altoPantalla * porcentajeHiloDibujado de alto<br>en x = hiloActual * grosorHilo, y = 0<br>color = urdimbre[hiloActual]"]
  U2 --> U3{"¿porcentajeHiloDibujado == 1?"}
  U3 -->|no| FIN
  U3 -->|sí| U4["Pasar al siguiente hilo<br>hiloActual + 1 · t0 = ahora"]
  U4 --> U5{"¿hiloActual == cantHilosUrdimbre?"}
  U5 -->|no| FIN
  U5 -->|sí| U6["fase = 'trama'<br>hiloActual = 0"]
  U6 --> FIN

  %% fase = trama
  F -->|trama| T1["porcentajeHiloDibujado =<br>min((ahora - t0) / tiempoHilo, 1)"]
  T1 --> T2["cantHilosDibujados =<br>cantHilosUrdimbre * porcentajeHiloDibujado,<br>redondeado hacia abajo<br>i = 0"]
  T2 --> T3{"¿i < cantHilosDibujados?"}
  T3 -->|sí| T4{"¿(hiloActual + i) % 2 == 0?"}
  T4 -->|sí| T5["No dibujar<br>la urdimbre va adelante"]
  T4 -->|no| T6["Dibujar cuadrado de grosorHilo de lado<br>en x = i * grosorHilo, y = hiloActual * grosorHilo<br>color = trama[hiloActual]"]
  T5 --> T7["i + 1"]
  T6 --> T7
  T7 --> T3
  T3 -->|no| T8{"¿porcentajeHiloDibujado == 1?"}
  T8 -->|no| FIN
  T8 -->|sí| T9["Pasar al siguiente hilo<br>hiloActual + 1 · t0 = ahora"]
  T9 --> T10{"¿hiloActual == cantHilosTrama?"}
  T10 -->|no| FIN
  T10 -->|sí| T11["fase = 'espera'"]
  T11 --> FIN

  %% fase = espera
  F -->|espera| E1{"¿pasaron tiempoEspera ms<br>desde t0?"}
  E1 -->|no| FIN
  E1 -->|sí| IP0

  FIN(["Terminar este frame"]) --> B0
