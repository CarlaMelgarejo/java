# java
package juego;

public class CuatroEnLinea {

	/**
	 * pre : 'filas' y 'columnas' son mayores o iguales a 4. post: empieza el
	 * juego entre el jugador que tiene fichas rojas, identificado como
	 * 'jugadorRojo' y el jugador que tiene fichas amarillas, identificado como
	 * 'jugadorAmarillo'. Todo el tablero está vacío.
	 * 
	 * @param filas
	 *            : cantidad de filas que tiene el tablero.
	 * @param columnas
	 *            : cantidad de columnas que tiene el tablero.
	 * @param jugadorRojo
	 *            : nombre del jugador con fichas rojas.
	 * @param jugadorAmarillo
	 *            : nombre del jugador con fichas amarillas.
	 */

	private int filasTablero;
	private int columnasTablero;
	private String jugadorRojo;
	private String jugadorAmarillo;
	private String turno;
	private String jugadorGanador;
	private Casillero[][] tablero;

	public CuatroEnLinea(int filas, int columnas, String jugadorRojo,
			String jugadorAmarillo) {

		if (filas < 4 || columnas < 4) {

			Error parametrosIncorrectos = new Error(
					"EL valor de las filas y columnas deben ser mayores o iguales a 4");
			throw parametrosIncorrectos;
		}

		this.filasTablero = filas;
		this.columnasTablero = columnas;
		this.jugadorRojo = jugadorRojo;
		this.jugadorAmarillo = jugadorAmarillo;
		this.turno = jugadorRojo;

		tablero = new Casillero[filas][columnas];

		for (int i = 0; i < tablero.length; i++) {

			for (int j = 0; j < tablero[i].length; j++) {

				tablero[i][j] = Casillero.VACIO;
			}
		}

	}

	/**
	 * post: devuelve el jugador que tiene el turno.
	 */
	public String obtenerTurno() {

		return turno;
	}

	/**
	 * post: devuelve el nombre del jugador Rojo.
	 * 
	 */
	public String obtenerJugadorRojo() {

		return jugadorRojo;
	}

	/**
	 * post: devuelve el nombre del jugador Amarillo.
	 */
	public String obtenerJugadorAmarillo() {

		return jugadorAmarillo;
	}

	/**
	 * post: devuelve la cantidad máxima de fichas que se pueden apilar en el
	 * tablero.
	 */
	public int contarFilas() {

		return filasTablero;
	}

	/**
	 * post: devuelve la cantidad máxima de fichas que se pueden alinear en el
	 * tablero.
	 */
	public int contarColumnas() {

		return columnasTablero;
	}

	/**
	 * pre : fila está en el intervalo [1, contarFilas()], columnas está en el*
	 * intervalo [1, contarColumnas()]. post: indica qué ocupa el casillero en
	 * la posición dada por fila y columna.
	 * 
	 * @param fila
	 * @param columna
	 */

	public Casillero obtenerCasillero(int fila, int columna) {

		if ((fila < 1) || (fila > contarFilas()) || (columna < 1)
				|| (columna > contarColumnas())) {

			Error parametrosIncorrectos = new Error(
					"El valor de filas y columnas se encuentra fuera del intervalo");
			throw parametrosIncorrectos;
		}

		return tablero[fila - 1][columna - 1];
	}

	/**
	 * pre : el juego no terminó, columna está en el intervalo [1,
	 * contarColumnas()] y aún queda un Casillero.VACIO en la columna indicada.
	 * post: deja caer una ficha en la columna indicada.
	 * 
	 * @param columna
	 */

	public void soltarFicha(int columna) {

		boolean puedeSoltar = false;

		if (columna < 1 || columna > contarColumnas()) {

			Error parametroIncorrecto = new Error(
					"El valor de columna se encutra fuera del intervalo");
			throw parametroIncorrecto;
		}

		for (int i = 0; i < contarFilas() && !termino() && !puedeSoltar; i++) {

			puedeSoltar = tablero[contarFilas() - i - 1][columna - 1] == Casillero.VACIO;

			if (puedeSoltar && turno == jugadorRojo) {

				tablero[contarFilas() - 1 - i][columna - 1] = Casillero.ROJO;
				turno = jugadorAmarillo;

			} else if (puedeSoltar && turno == jugadorAmarillo) {

				tablero[contarFilas() - 1 - i][columna - 1] = Casillero.AMARILLO;
				turno = jugadorRojo;
			}
		}

	}

	/**
	 * post: indica si el juego terminó porque uno de los jugadores ganó o no
	 * existen casilleros vacíos.
	 */

	public boolean termino() {

		boolean partidaTerminada = false;
		boolean tableroLleno = true;

		for (int i = 0; i < contarColumnas(); i++) {
			if (tablero[0][i] == Casillero.VACIO) {
				tableroLleno = false;
				i = contarColumnas();
			}
		}
		partidaTerminada = hayGanador() || tableroLleno;
		return partidaTerminada;
	}

	/**
	 * post: indica si el juego terminó y tiene un ganador.
	 */

	public boolean hayGanador() {

		boolean hayGanador = false;

		for (int i = 0; i < contarFilas() && !hayGanador; i++) {

			for (int j = 0; j < contarColumnas() - 3 && !hayGanador; j++) {

				if ((tablero[i][j] == tablero[i][j + 1])
						&& (tablero[i][j] == tablero[i][j + 2])
						&& (tablero[i][j] == tablero[i][j + 3])
						&& (tablero[i][j] != Casillero.VACIO)) {
					hayGanador = true;
					if (tablero[i][j] == Casillero.AMARILLO) {
						jugadorGanador = jugadorAmarillo;
					} else {
						jugadorGanador = jugadorRojo;
					}
				}
			}
		}

		for (int j = 0; j < contarColumnas() && !hayGanador; j++) {

			for (int i = 0; i < contarFilas() - 3 && !hayGanador; i++) {

				if ((tablero[i][j] == tablero[i + 1][j])
						&& (tablero[i][j] == tablero[i + 2][j])
						&& (tablero[i][j] == tablero[i + 3][j])
						&& (tablero[i][j] != Casillero.VACIO)) {
					hayGanador = true;
					if (tablero[i][j] == Casillero.AMARILLO) {
						jugadorGanador = jugadorAmarillo;
					} else {
						jugadorGanador = jugadorRojo;
					}
				}
			}
		}

		for (int i = 3; i < contarFilas() && !hayGanador; i++) {

			for (int j = 0; j < contarColumnas() - 3 && !hayGanador; j++) {

				if ((tablero[i][j] == tablero[i - 1][j + 1])
						&& (tablero[i][j] == tablero[i - 2][j + 2])
						&& (tablero[i][j] == tablero[i - 3][j + 3])
						&& (tablero[i][j] != Casillero.VACIO)) {
					hayGanador = true;
					if (tablero[i][j] == Casillero.AMARILLO) {
						jugadorGanador = jugadorAmarillo;
					} else {
						jugadorGanador = jugadorRojo;
					}
				}
			}
		}

		for (int i = 0; i < contarFilas() - 3 && !hayGanador; i++) {

			for (int j = 0; j < contarColumnas() - 3 && !hayGanador; j++) {

				if ((tablero[i][j] == tablero[i + 1][j + 1])
						&& (tablero[i][j] == tablero[i + 2][j + 2])
						&& (tablero[i][j] == tablero[i + 3][j + 3])
						&& (tablero[i][j] != Casillero.VACIO)) {
					hayGanador = true;
					if (tablero[i][j] == Casillero.AMARILLO) {
						jugadorGanador = jugadorAmarillo;
					} else {
						jugadorGanador = jugadorRojo;
					}
				}
			}
		}

		return hayGanador;
	}

	/**
	 * pre : el juego terminó porque hubo un ganador. post: devuelve el nombre
	 * del jugador que ganó el juego.
	 */

	public String obtenerGanador() {

		if (!hayGanador()) {
			Error noExisteGanador = new Error(
					"El juego no tiene ningun ganador aun o termino por estar el tablero lleno");
			throw noExisteGanador;
		}
		return jugadorGanador;
	}

}
