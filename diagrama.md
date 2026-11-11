flowchart TD
  %% ---------- INICIO ----------
  I0([INICIO]) --> P["PARÁMETROS INICIALES:<br><br>grosorHilo<br>nColores<br>nMin · nMax<br>tiempoHilo<br>tiempoEspera<br>paletaColores<br>colorFondo<br>cantHilosUrdimbre = anchoPantalla / grosorHilo, redondeado hacia arriba<br>cantHilosTrama = altoPantalla / grosorHilo, redondeado hacia arriba"]
  P --> I1["Pintar <b>colorFondo</b> en todo el<br>canvas una única vez"]
  subgraph IP [" "]
    IP0(["InicializarPatron()"]) --> IP1["DEFINIR COLORES<br>Elegir <b>nColores</b> al azar de <b>paletaColores</b><br><b>colorAcento</b> = posición 0<br><b>coloresBase</b> = posición 1 en adelante"]
    IP1 --> IP2["DEFINIR FÓRMULA N, K, A<br><b>n</b> aleatoriamente entre <b>nMin</b> y <b>nMax</b><br><b>k</b> aleatoriamente entre 2 y <b>n</b> - 1<br><b>a</b> aleatoriamente entre 1 y <b>k</b> - 1"]
    IP2 --> IP3["DEFINIR SECUENCIA BASE<br><b>secuenciaInicial</b> = <b>SecuenciaEuclides(k, n)</b><br>en el paso i va 1 si (i * k) % n < k"]
    IP3 --> IP4["DEFINIR SECUENCIA FINAL (CON ACENTO)<br><b>secuencia</b> = <b>AgregarAcento(secuenciaInicial, k, a)</b><br>se reparte <b>E(a, k)</b> sobre los k unos (1)<br>los 1 marcados pasan a 2"]

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
    IPU["urdimbre = secuenciaColores"]
    IPT["trama = secuenciaColores"]
    C7 --> IPU
    C7 --> IPT
    IPU --> IP6
    IPT --> IP6
  end

  I1 --> IP0

  %% ---------- BUCLE ----------
  subgraph BU [" "]
    B0(["BUCLE<br>se repite cada frame"])
    B1["ahora = tiempo actual"]
    F{"¿fase?"}
    U1["porcentajeHiloDibujado =<br>min((ahora - t0) / tiempoHilo, 1)"]
    T1["porcentajeHiloDibujado =<br>min((ahora - t0) / tiempoHilo, 1)"]
    E1{"¿pasaron tiempoEspera ms<br>desde t0?"}

    B0 --> B1
    B1 --> F
    F -->|urdimbre| U1
    F -->|trama| T1
    F -->|espera| E1

    %% fase = urdimbre
    U1 --> U2["Dibujar urdimbre hiloActual<br>rectángulo de grosorHilo de ancho<br>y altoPantalla * porcentajeHiloDibujado de alto<br>en x = hiloActual * grosorHilo, y = 0<br>color = urdimbre[hiloActual]"]
    U2 --> U3{"¿porcentajeHiloDibujado == 1?"}
    U3 -->|no| FIN
    U3 -->|sí| U4["Pasar al siguiente hilo<br>hiloActual + 1 · t0 = ahora"]
    U4 --> U5{"¿hiloActual == cantHilosUrdimbre?"}
    U5 -->|no| FIN
    U5 -->|sí| U6["fase = 'trama'<br>hiloActual = 0"]
    U6 --> FIN

    %% fase = trama
    T1 --> T2["cantCrucesADibujar =<br>cantHilosUrdimbre * porcentajeHiloDibujado,<br>redondeado hacia abajo<br>i = 0"]
    T2 --> T3{"¿i < cantCrucesADibujar?"}
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
    E1 -->|no| FIN

    FIN(["Terminar este frame<br>y volver a BUCLE"])
  end

  IP6 --> B0
  E1 -->|sí| IP0

  style B0 font-size:22px,font-weight:bold
