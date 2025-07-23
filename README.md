# Parques-grupo-16

Integrantes del Equipo
-
- CEDEÑO SABOGAL, SANTIAGO            scedeno@unal.edu.co
- BAYONA MARIÑO, ERICK SANTIAGO       ebayonam@unal.edu.co
- LADINO NARVAEZ, JOSE MANUEL         jladinon@unal.edu.co
- BOHADA LOPEZ, SANTIAGO              sbohada@unal.edu.co

Instrucciones para Ejecutar el Programa

1. Requisitos:
   - Tener donde correr un código de python.

2. Ejecutar el Programa:
   - Copia el código proporcionado a continuación y pégalo directamente en tu entorno de desarrollo Python o editor de texto.
   - Ejecuta el código en tu entorno Python.

Enlace al Video

https://drive.google.com/file/d/1zYmjHJQJn3vWBB1meJA-xVxzGt0rBBmy/view?usp=sharing

Explicación del Código

Variables Globales

- casillas: Define el número total de casillas en el tablero (predeterminado: 68).
- fichas_jugador: Número de fichas por jugador (predeterminado: 4).
- entrada_casillas: Diccionario que mapea el color del jugador a las casillas de entrada.
- salida_casillas: Diccionario que mapea el color del jugador a la casilla de salida.
- entrada_posiciones: Diccionario que mapea el color del jugador a la posición inicial de entrada en el tablero.
- seguros: Diccionario que mapea el color del jugador a las casillas de seguro.
- jugadores: Lista de jugadores que participan en el juego.
- fichas: Diccionario que asocia a cada jugador con la lista de sus fichas. Inicialmente, todas las fichas están en la casilla de salida.
- fichas_protegidas: Diccionario que indica si una ficha está protegida en una casilla de seguro.
- turno: Controla el turno actual del juego. Comienza en 0 y se incrementa conforme avanza el juego.

Funciones

`tirar_dados()`
def tirar_dados():
    dado1 = random.randint(1, 6)
    dado2 = random.randint(1, 6)
    return dado1, dado2

#Esta función simula el lanzamiento de dos dados. Utiliza la función randint del módulo random para generar dos números aleatorios entre 1 y 6 (inclusive), que representan los valores de los dados.
#Retorno: Devuelve dos números enteros, uno para cada dado.




`mostrar_animacion_dados()`
def mostrar_animacion_dados():
    for _ in range(3):
        sys.stdout.write("\rLanzando dados... 🎲🎲")
        sys.stdout.flush()
        time.sleep(0.3)
        sys.stdout.write("\r                 ")
        sys.stdout.flush()
        time.sleep(0.1)

#Esta función muestra una animación simple de lanzamiento de dados para hacer la experiencia de juego más dinámica.



`mover_ficha(jugador, ficha_idx, pasos, dado1, dado2)`
def mover_ficha(jugador, ficha_idx, pasos, dado1, dado2):
    posicion = fichas[jugador][ficha_idx]
    
    if isinstance(posicion, str) and posicion.endswith(jugador[:2]):
        num = int(posicion[:-2])
        nueva_posicion = num + pasos
        if nueva_posicion <= 8:
            fichas[jugador][ficha_idx] = f"{nueva_posicion}{jugador[:2]}"
            print(f"{jugador} movió su ficha {ficha_idx + 1} a {fichas[jugador][ficha_idx]}.")
        else:
            print(f"{jugador} no puede mover su ficha {ficha_idx + 1} más allá de la casilla final.")
    else:
        posicion_nueva = (posicion + pasos - 1) % casillas + 1

        if posicion in seguros.values():
            idx = jugadores.index(jugador)
            fichas_protegidas[jugador][fichas[jugador].index(posicion)] = True

        if posicion in seguros.values() and posicion != posicion_nueva:
            fichas_protegidas[jugador][fichas[jugador].index(posicion)] = False

        for otro_jugador in jugadores:
            if otro_jugador != jugador:
                if posicion_nueva in fichas[otro_jugador]:
                    ficha_ocupada_idx = fichas[otro_jugador].index(posicion_nueva)
                    if ficha_ocupada_idx is not None:
                        if fichas_protegidas[otro_jugador][ficha_ocupada_idx]:
                            print(f"La ficha del {otro_jugador} en la casilla {posicion_nueva} está protegida y no puede ser comida.")
                        else:
                            print(f"{jugador} comió la ficha del {otro_jugador} en la casilla {posicioniva}.")
                            fichas[otro_jugador][ficha_ocupada_idx] = salida_casillas[otro_jugador]
                            fichas_protegidas[otro_jugador][ficha_ocupada_idx] = False
                        break
        
        if posicion in seguros.values() and posicion_nueva in seguros.values():
            print(f"{jugador} no puede mover su ficha {ficha_idx + 1} a otra casilla de seguro.")
            return
        
        if posicion_nueva == seguros[jugador]:
            fichas_protegidas[jugador][fichas[jugador].index(posicion_nueva)] = True
            print(f"{jugador} llegó a una casilla de seguro en {posicion_nueva} y está protegida.")
        
        if posicion_nueva == entrada_posiciones[jugador]:
            fichas[jugador][ficha_idx] = entrada_casillas[jugador][0]
            fichas_protegidas[jugador][ficha_idx] = False
            print(f"{jugador} entró a sus casillas de entrada con su ficha {ficha_idx + 1} en {fichas[jugador][ficha_idx]}.")
        elif posicion_nueva <= casillas:
            fichas[jugador][ficha_idx] = posicion_nueva
            print(f"{jugador} movió su ficha {ficha_idx + 1} a la casilla {posicion_nueva}.")
        else:
            print(f"{jugador} no puede mover su ficha {ficha_idx + 1} más allá de la casilla {casillas}.")


#Maneja el movimiento de una ficha específica de un jugador, gestionando el desplazamiento en el tablero, la captura de fichas o la protección en casillas de seguro.
#Si la ficha está en la posición 0 (en la cárcel): La ficha solo puede salir si al menos uno de los dados muestra un 5, o la suma de ambos dados es 5. Si se cumple esta condición, la ficha se mueve a la posición correspondiente. Si no, el jugador no puede sacar la ficha.
#Si la ficha ya está en el tablero: La función calcula la nueva posición de la ficha sumando los pasos dados. Si la nueva posición es válida (es decir, no excede el número total de casillas), actualiza la posición de la ficha; de lo contrario, indica que el movimiento no es posible.



`mostrar_posiciones()`
def mostrar_posiciones():
    print("\nposiciones de fichas:")
    for jugador in jugadores:
        print(f"{jugador}: {fichas[jugador]}")
    print()

#Imprime en la consola la posición actual de todas las fichas de cada jugador. Muestra la lista de posiciones de fichas para cada jugador.



`jugar()`
def jugar():
    global turno
    fin_juego = False

    while not fin_juego:
        for i in range(len(jugadores)):
            jugador = jugadores[turno]
            print(f"\nTurno de {jugador}.")
            
            mostrar_animacion_dados()
            dado1, dado2 = tirar_dados()
            print(f"Dados: {dado1}, {dado2}")

            movimientos_posibles = []
            for j in range(fichas_jugador):
                posicion = fichas[jugador][j]
                if posicion == salida_casillas[jugador]:
                    if dado1 == 5 or dado2 == 5 or (dado1 + dado2) == 5:
                        movimientos_posibles.append((j, dado1 + dado2))
                else:
                    movimientos_posibles.append((j, dado1))
                    movimientos_posibles.append((j, dado2))
            
            if movimientos_posibles:
                print("Movimientos posibles:")
                for idx, (ficha_idx, pasos) in enumerate(movimientos_posibles):
                    print(f"{idx + 1}. Mover ficha {ficha_idx + 1} {pasos} pasos")
                
                try:
                    eleccion = int(input("Elija un movimiento: ")) - 1
                    if 0 <= eleccion < len(movimientos_posibles):
                        mover_ficha(jugador, movimientos_posibles[eleccion][0], movimientos_posibles[eleccion][1], dado1, dado2)
                    else:
                        print("Elección no válida.")
                except ValueError:
                    print("Entrada no válida. Debe ingresar un número.")
            else:
                print("No hay posibles movimientos. Fin del turno.")
            
            if any(pos == entrada_casillas[jugador][-1] for pos in fichas[jugador]):
                print(f"{jugador} ganó el juego!")
                fin_juego = True
                break
            
            turno = (turno + 1) % len(jugadores)
        
        if not fin_juego:
            mostrar_posiciones()


#Controla la lógica principal del juego.
#Turnos: Cada jugador toma su turno, en el cual se lanzan los dados y se muestran los valores obtenidos.
#Movimientos posibles: Para cada ficha del jugador, se determinan los movimientos posibles basados en el resultado de los dados. Se añaden a una lista los movimientos válidos.
#Elección del movimiento: El jugador elige uno de los movimientos posibles mediante una entrada numérica. La ficha se mueve según la elección del jugador.
#Ganador: Se verifica si el jugador ha ganado (si todas sus fichas han alcanzado o superado el número total de casillas). Si un jugador gana, se finaliza el juego.
#Cambio de turno: Se actualiza el turno para el siguiente jugador.



`inicio()`
def inicio():
    print("Bienvenido a Parqués")
    print("Deseas jugar?")
    print("1. Sí")
    print("2. No")

    eleccion = input("Elige una opción: ")

    if eleccion == "1":
        jugar()
    else:
        print("Has elegido no jugar. Hasta la próxima.")

#Muestra el menú inicial y permite al usuario elegir si desea jugar al juego de Parqués o salir.
