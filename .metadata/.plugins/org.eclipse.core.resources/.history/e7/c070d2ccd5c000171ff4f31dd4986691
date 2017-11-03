package mundo;

import java.awt.Graphics;

import estados.Estado;
import juego.Juego;

public class Mundo {
	private Juego juego;
	private int ancho;
	private int alto;
	private int spawnX;
	private int spawnY;
	private int xOffset;
	private int yOffset;

	private float[] iso = new float[2];
	private int[][] tiles;
	private int[][] tilesInv;

	private int xMinimo;
	private int xMaximo;
	private int yMinimo;
	private int yMaximo;

	private Grafo grafoDeTilesNoSolidos;

	public Mundo(Juego juego, String pathMap, String pathObstac) {
		this.juego = juego;
		cargarMundo(pathMap, pathObstac);
		mundoAGrafo();
	}

	public void actualizar() {

	}

	public void graficar(Graphics g) {
		xOffset = juego.getEstadoJuego().getPersonaje().getxOffset();
		yOffset = juego.getEstadoJuego().getPersonaje().getYOffset();

		xMinimo = (int) (juego.getCamara().getxOffset() - xOffset - 30);
		xMaximo = xMinimo + juego.getAncho() + xOffset + 30;
		yMinimo = (int) juego.getCamara().getyOffset() + yOffset - 60;
		yMaximo = yMinimo + juego.getAlto() + yOffset + 60;

		// Grafico el el tile base
		for (int i = 0; i < alto; i++) {
			for (int j = 0; j < ancho; j++) {
				iso = dosDaIso(j, i);
				if ((iso[0] >= xMinimo && iso[0] <= xMaximo) && (iso[1] >= yMinimo && iso[1] <= yMaximo)) {
					int map = juego.getPersonaje().getMapa();
					if (map == 1) {
						Tile.aubenor[Tile.aubenorBase].graficar(g, (int) (iso[0] - juego.getCamara().getxOffset()),
								(int) (iso[1] - juego.getCamara().getyOffset() - 32),64,64);
					} else if (map == 2) {
						Tile.aris[Tile.arisBase].graficar(g, (int) (iso[0] - juego.getCamara().getxOffset()),
								(int) (iso[1] - juego.getCamara().getyOffset() - 32),64,64);
					} else if (map == 3) {
						Tile.aubenor[Tile.aubenorBase].graficar(g, (int) (iso[0] - juego.getCamara().getxOffset()),
								(int) (iso[1] - juego.getCamara().getyOffset() - 32),64,64);
					}
					if(!getTile(j,i).esSolido())
						getTile(j,i).graficar(g, (int) (iso[0] - juego.getCamara().getxOffset()),
								(int) (iso[1] - juego.getCamara().getyOffset() - 32 ),64,64);
				}
			}
		}
	}

	public void graficarObstaculos(Graphics g) {
		Tile obst;
		for (int i = 0; i < alto; i++) {
			for (int j = 0; j < ancho; j++) {
				iso = dosDaIso(j, i);
				// Grafico al personaje
				if(Estado.getEstado() == juego.getEstadoJuego())
					if (Mundo.mouseATile(juego.getUbicacionPersonaje().getPosX(),
							juego.getUbicacionPersonaje().getPosY())[0] == j
							&& Mundo.mouseATile(juego.getUbicacionPersonaje().getPosX(),
									juego.getUbicacionPersonaje().getPosY())[1] == i )
						juego.getEstadoJuego().getPersonaje().graficar(g);

				// Grafico los obstaculos
				if ((iso[0] >= xMinimo && iso[0] <= xMaximo) && (iso[1] >= yMinimo && iso[1] <= yMaximo)
						&& getTile(j, i).esSolido()) {
					obst = getTile(j, i);
					obst.graficar(g, (int) (iso[0] - juego.getCamara().getxOffset()),
							(int) (iso[1] - juego.getCamara().getyOffset() - obst.getAlto()/2), obst.getAncho(),
							obst.getAlto());
				}
			}
		}
	}

	public Tile getTile(int x, int y) {
		Tile t = Tile.tiles[tiles[x][y]];
		if (t == null) {
			int map = juego.getPersonaje().getMapa();
			if (map == 1) {
				return Tile.aubenor[Tile.aubenorBase];
			} else if (map == 2) {
				return Tile.aris[Tile.arisBase];
			} else if (map == 3) {
				return Tile.aubenor[Tile.aubenorBase];
			}
		}
		return t;
	}

	private void cargarMundo(String pathMapa, String pathObstaculos) {
		String archivo = Utilitarias.archivoAString(pathMapa);
		String[] tokens = archivo.split("\\s+");
		ancho = Utilitarias.parseInt(tokens[0]);
		alto = Utilitarias.parseInt(tokens[1]);
		spawnX = Utilitarias.parseInt(tokens[2]);
		spawnY = Utilitarias.parseInt(tokens[3]);

		tiles = new int[ancho][alto];
		tilesInv = new int[alto][ancho];

		for (int y = 0; y < alto; y++) {
			for (int x = 0; x < ancho; x++) {

				tiles[x][y] = Utilitarias.parseInt(tokens[(x + y * ancho + 4)]);
				tilesInv[y][x] = tiles[x][y];
			}
		}

	}

	private void mundoAGrafo() {
		// Creo una matriz de nodos
		Nodo[][] nodos = new Nodo[ancho][alto];
		int indice = 0;
		// Lleno la matriz con los nodos
		for (int y = 0; y < alto; y++)
			for (int x = 0; x < ancho; x++)
				nodos[y][x] = new Nodo(indice++, x, y);
		// Variables finales
		int xFinal = ancho;
		int yFinal = alto;
		// Uno cada nodo con sus adyacentes
		for (int x = 0; x < yFinal; x++) {
			for (int y = 0; y < xFinal; y++) {
				if (!Tile.tiles[tilesInv[x][y]].esSolido()) {
					// Si no es la ultima fila y el tile de abajo es no solido,
					// lo uno
					if (y < yFinal - 1 && !Tile.tiles[tilesInv[x][y + 1]].esSolido()) {
						nodos[x][y].agregarAdyacente(nodos[x][y + 1]);
						nodos[x][y + 1].agregarAdyacente(nodos[x][y]);
					}
					// Si no es la ultima columna
					if (x < xFinal - 1) {
						// Si el de arriba a la derecha no es un tile solido
						// Y ademas el de arriba ni el de la derecha lo son, lo
						// uno
						// Tiene que ser a partir de la segunda fila
						if (y > 0 && !Tile.tiles[tilesInv[x + 1][y - 1]].esSolido()
								&& !Tile.tiles[tilesInv[x + 1][y]].esSolido()
								&& !Tile.tiles[tilesInv[x][y - 1]].esSolido()) {
							nodos[x][y].agregarAdyacente(nodos[x + 1][y - 1]);
							nodos[x + 1][y - 1].agregarAdyacente(nodos[x][y]);
						}
						// Si el de la derecha no es un tile solido lo uno
						if (!Tile.tiles[tilesInv[x + 1][y]].esSolido()) {
							nodos[x][y].agregarAdyacente(nodos[x + 1][y]);
							nodos[x + 1][y].agregarAdyacente(nodos[x][y]);
						}
						// Si el de abajo a la derecha no es un tile solido
						// Y ademas el de abajo ni el de la derecha lo son, lo
						// uno
						// Debe ser antes de la ultima fila
						if (y < yFinal - 1 && !Tile.tiles[tilesInv[x + 1][y + 1]].esSolido()
								&& !Tile.tiles[tilesInv[x + 1][y]].esSolido()
								&& !Tile.tiles[tilesInv[x][y + 1]].esSolido()) {
							nodos[x][y].agregarAdyacente(nodos[x + 1][y + 1]);
							nodos[x + 1][y + 1].agregarAdyacente(nodos[x][y]);
						}
					}
				}
			}
		}
		// Creo un grafo para almacenar solo los tiles no solidos
		grafoDeTilesNoSolidos = new Grafo(ancho * alto);
		indice = 0;
		// Paso la matriz a un array
		for (int i = 0; i < ancho; i++)
			for (int j = 0; j < alto; j++)
				grafoDeTilesNoSolidos.agregarNodo(nodos[i][j]);
	}

	public Grafo obtenerGrafoDeTilesNoSolidos() {
		return grafoDeTilesNoSolidos;
	}

	public int obtenerAncho() {
		return ancho;
	}

	public int obtenerAlto() {
		return alto;
	}

	public static float[] isoA2D(float x, float y) {
		float[] dosD = new float[2];

		dosD[0] = (x / (Tile.ANCHO / 2) + y / (Tile.ALTO / 2)) / 2;
		dosD[1] = (y / (Tile.ALTO / 2) - (x / (Tile.ANCHO / 2))) / 2;

		return dosD;
	}

	public static float[] dosDaIso(float x, float y) {
		float[] iso = new float[2];

		iso[0] = (x - y) * (Tile.ANCHO / 2);
		iso[1] = (x + y) * (Tile.ALTO / 2);

		return iso;
	}

	public static int[] mouseATile(float x, float y) {
		int tile[] = new int[2];

		tile[0] = (int) Math.floor((y / Tile.ALTO) + (x / Tile.ANCHO)) + 1;
		tile[1] = (int) Math.floor((-x / Tile.ANCHO) + (y / Tile.ALTO)) + 1;

		return tile;
	}
}