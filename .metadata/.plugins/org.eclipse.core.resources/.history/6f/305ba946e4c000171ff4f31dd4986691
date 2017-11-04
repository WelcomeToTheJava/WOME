package entidades;

import java.awt.Color;
import java.awt.Font;
import java.awt.Graphics;
import java.awt.image.BufferedImage;
import java.io.IOException;
import java.util.Iterator;
import java.util.LinkedList;

import javax.swing.JOptionPane;

import com.google.gson.Gson;

import chat.VentanaContactos;
import estados.Estado;
import frames.MenuEscape;
import frames.MenuInventario;
import interfaz.MenuInfoPersonaje;
import juego.Juego;
import juego.Pantalla;
import mensajeria.PaqueteBatalla;
import mensajeria.PaqueteComerciar;
import mensajeria.PaqueteMovimiento;
import mundo.Grafo;
import mundo.Mundo;
import mundo.Nodo;
import recursos.Recursos;
/**Clase Entidad
 */
public class Entidad {

	Juego juego;

	// Tamaño de la entidad
	private int ancho;
	private int alto;

	// Posiciones
	private float x;
	private float y;
	private float dx;
	private float dy;
	private float xInicio;
	private float yInicio;
	private float xFinal;
	private float yFinal;
	private int xOffset;
	private int yOffset;
	private int drawX;
	private int drawY;
	private int []posMouseRecorrido;
	private int []posMouse;
	private int[] tile;

	// Movimiento Actual
	private static final int horizontalDer = 4;
	private static final int horizontalIzq = 0;
	private static final int verticalSup = 2;
	private static final int verticalInf = 6;
	private static final int diagonalInfIzq = 7;
	private static final int diagonalInfDer = 5;
	private static final int diagonalSupDer = 3;
	private static final int diagonalSupIzq = 1;
	private int movimientoHacia = 6;
	private boolean enMovimiento;

	// Animaciones
	private final Animacion moverIzq;
	private final Animacion moverArribaIzq;
	private final Animacion moverArriba;
	private final Animacion moverArribaDer;
	private final Animacion moverDer;
	private final Animacion moverAbajoDer;
	private final Animacion moverAbajo;
	private final Animacion moverAbajoIzq;

	private final Gson gson = new Gson();
	private int intervaloEnvio = 0;

	// pila de movimiento
	private PilaDeTiles pilaMovimiento;
	private int[] tileActual;
	private int[] tileFinal;
	private int[] tileMoverme;

	private Mundo mundo;
	private String nombre;
	private int[] tilePersonajes;
	private int idEnemigo;
	
	//Ubicacion para abrir comerciar.
	private float xComercio;
	private float yComercio;
	private float [] comercio;
	
	/**Constructor de la clase Entidad
	 * @param juego juego con el que se instancia Entidad
	 * @param mundo mundo con el que se instancia Entidad
	 * @param ancho ancho
	 * @param alto alto
	 * @param nombre nombre del personaje
	 * @param spawnX tile X donde spawnea
	 * @param spawnY tile Y donde spawnea
	 * @param animaciones animaciones del personaje
	 * @param velAnimacion velocidad de animacion del personaje
	 */
	public Entidad(final Juego juego, final Mundo mundo, final int ancho, final int alto, 
			final String nombre, final float spawnX, final float spawnY,
			final LinkedList<BufferedImage[]> animaciones, final int velAnimacion) {
		this.juego = juego;
		this.ancho = ancho;
		this.alto = alto;
		this.nombre = nombre;
		this.mundo = mundo;
		xOffset = ancho / 2;
		yOffset = alto / 2;
		x = (int) (spawnX / 64) * 64;
		y = (int) (spawnY / 32) * 32;

		moverIzq = new Animacion(velAnimacion, animaciones.get(0));
		moverArribaIzq = new Animacion(velAnimacion, animaciones.get(1));
		moverArriba = new Animacion(velAnimacion, animaciones.get(2));
		moverArribaDer = new Animacion(velAnimacion, animaciones.get(3));
		moverDer = new Animacion(velAnimacion, animaciones.get(4));
		moverAbajoDer = new Animacion(velAnimacion, animaciones.get(5));
		moverAbajo = new Animacion(velAnimacion, animaciones.get(6));
		moverAbajoIzq = new Animacion(velAnimacion, animaciones.get(7));

		// Informo mi posicion actual
		juego.getUbicacionPersonaje().setPosX(x);
		juego.getUbicacionPersonaje().setPosY(y);
		juego.getUbicacionPersonaje().setDireccion(getDireccion());
		juego.getUbicacionPersonaje().setFrame(getFrame());
	}
	/**Actualiza el personaje
	 */
	public void actualizar() {

		if (enMovimiento) {
			moverIzq.actualizar();
			moverArribaIzq.actualizar();
			moverArriba.actualizar();
			moverArribaDer.actualizar();
			moverDer.actualizar();
			moverAbajoDer.actualizar();
			moverAbajo.actualizar();
			moverAbajoIzq.actualizar();
		} else {
			moverIzq.reset();
			moverArribaIzq.reset();
			moverArriba.reset();
			moverArribaDer.reset();
			moverDer.reset();
			moverAbajoDer.reset();
			moverAbajo.reset();
			moverAbajoIzq.reset();
		}

		getEntrada();
		mover();

		juego.getCamara().Centrar(this);
	}
	/**Devuelve la entrada
	 */
	public void getEntrada() {
		posMouseRecorrido = juego.getHandlerMouse().getPosMouseRecorrido();
		posMouse = juego.getHandlerMouse().getPosMouse();
		if(juego.getHandlerMouse().getNuevoClick() && posMouse[0] >= 738 && posMouse[0] <= 797  && posMouse[1] >= 545 && posMouse[1] <= 597) {
			if (Pantalla.menuInventario == null) {
				Pantalla.menuInventario = new MenuInventario(juego.getCliente());
				Pantalla.menuInventario.setVisible(true);
			}
			juego.getHandlerMouse().setNuevoClick(false);				
		}
		if(juego.getHandlerMouse().getNuevoClick() && posMouse[0] >= 3 && posMouse[0] <= 105 && posMouse[1] >= 562 && posMouse[1] <= 597) {
			if (Pantalla.menuEscp == null) {
				Pantalla.menuEscp = new MenuEscape(juego.getCliente());
				Pantalla.menuEscp.setVisible(true);
			}
			juego.getHandlerMouse().setNuevoClick(false);				
		}
		if(juego.getHandlerMouse().getNuevoClick() && posMouse[0] >= 3 && posMouse[0] <= 105 && posMouse[1] >= 524 && posMouse[1] <= 559) {
			if (Pantalla.ventContac == null) {
				Pantalla.ventContac = new VentanaContactos(juego);
				Pantalla.ventContac.setVisible(true);
			}
			juego.getHandlerMouse().setNuevoClick(false);				
		}
		// Tomo el click izquierdo 
		if (juego.getHandlerMouse().getNuevoClick()) {
			if (juego.getEstadoJuego().getHaySolicitud()) {

				if (juego.getEstadoJuego().getMenuEnemigo().clickEnMenu(posMouse[0], posMouse[1])) {
					if (juego.getEstadoJuego().getMenuEnemigo().clickEnBoton(posMouse[0], 
							posMouse[1])) {
						// Pregunto si menuBatallar o menuComerciar, sino no me interesa hacer esto
						if (juego.getEstadoJuego().getTipoSolicitud() == 
								MenuInfoPersonaje.menuBatallar || 
								juego.getEstadoJuego().getTipoSolicitud() == 
								MenuInfoPersonaje.menuComerciar) {
							//Guardo las poss con el que quiero comerciar
							xComercio = juego.getUbicacionPersonajes().get(idEnemigo).getPosX();
							yComercio = juego.getUbicacionPersonajes().get(idEnemigo).getPosY();
							comercio = Mundo.isoA2D(xComercio, yComercio);							
						}
						// pregunto si el menu emergente es de tipo batalla
						if (juego.getEstadoJuego().getTipoSolicitud() == 
								MenuInfoPersonaje.menuBatallar) {
							//ME FIJO SI CON EL QUE QUIERO BATALLAR ESTA EN LA ZONA DE COMERCIO
							if (!((int)comercio[0] >= 44 && (int)comercio[0] <= 71 && 
									(int)comercio[1] >= 0 && (int)comercio[1] <= 29)) {
								juego.getEstadoJuego().setHaySolicitud(false, null, MenuInfoPersonaje.menuBatallar);
								PaqueteBatalla pBatalla = new PaqueteBatalla();
								
								pBatalla.setId(juego.getPersonaje().getId());
								pBatalla.setIdEnemigo(idEnemigo);
								
								juego.getEstadoJuego().setHaySolicitud(false, null, MenuInfoPersonaje.menuBatallar);
								
								try {
									juego.getCliente().getSalida().writeObject(gson.toJson
											(pBatalla));
								} catch (IOException e) {
									JOptionPane.showMessageDialog(null, "Fallo la conexión "
											+ "con el servidor");
								}
							} else {
								JOptionPane.showMessageDialog(null, "El otro usuario se encuentra "
										+ "dentro de la zona de comercio");	
							}				
						} else {
							// PREGUNTO SI EL MENU EMERGENTE ES DE TIPO COMERCIO
							if (juego.getEstadoJuego().getTipoSolicitud() == 
									MenuInfoPersonaje.menuComerciar) {
								if ((int)comercio[0] >= 44 && (int)comercio[0] <= 71 && 
										(int)comercio[1] >= 0 && (int)comercio[1] <= 29) {
									if (juego.getCliente().getM1() == null) {
										juego.getCliente().setPaqueteComercio(new PaqueteComerciar());
										juego.getCliente().getPaqueteComercio().setId(juego.getPersonaje().getId());
										juego.getCliente().getPaqueteComercio().setIdEnemigo(idEnemigo);
										
										try {
											juego.getCliente().getSalida().writeObject(gson.toJson
													(juego.getCliente().getPaqueteComercio()));
										} catch (IOException e) {
											JOptionPane.showMessageDialog(null, "Fallo la conexión "
													+ "con el servidor");
										}	
									} else {
										JOptionPane.showMessageDialog(null, "Ya te encuentras comerciando!");
									}
								} else {
									JOptionPane.showMessageDialog(null, "El otro usuario no se encuentra "
											+ "dentro de la zona de comercio");
								}
							}
						}
						juego.getEstadoJuego().setHaySolicitud(false, null, MenuInfoPersonaje.menuBatallar);


					} else if (juego.getEstadoJuego().getMenuEnemigo().clickEnCerrar(
							posMouse[0], posMouse[1])) {
						juego.getEstadoJuego().setHaySolicitud(false, null, MenuInfoPersonaje.menuBatallar);
					}
				} else {
					juego.getEstadoJuego().setHaySolicitud(false, null, MenuInfoPersonaje.menuBatallar);
				}
			} else {
				Iterator<Integer> it = juego.getUbicacionPersonajes().
						keySet().iterator();
				int key;
				int []tileMoverme = Mundo.mouseATile(posMouse[0] + juego.getCamara().getxOffset() - 
						xOffset,posMouse[1] + juego.getCamara().getyOffset() - yOffset);
				PaqueteMovimiento actual;

				while (it.hasNext()) {
					key = it.next();
					actual = juego.getUbicacionPersonajes().get(key);
					tilePersonajes = Mundo.mouseATile(actual.getPosX(), actual.getPosY());
					if (actual != null && actual.getIdPersonaje() != juego.getPersonaje().getId()
							&& juego.getPersonajesConectados().get(actual.
									getIdPersonaje()) != null
							&& juego.getPersonajesConectados().get(actual.
									getIdPersonaje()).getEstado() == Estado.estadoJuego) {

						if (tileMoverme[0] == tilePersonajes[0] && tileMoverme[1] == 
								tilePersonajes[1]) {
							idEnemigo = actual.getIdPersonaje();
							float XY[] = Mundo.isoA2D(x,y);
							// ESTA ESTE PARA NO MOVERME HASTA EL LUGAR.
							if(XY[0] >= 44 && XY[0] <= 71 && XY[1] >= 0 && XY[1] <= 29) {
								// SI ESTOY DENTRO DE LA ZONA DE COMERCIO SETEO QUE SE ABRA EL MENU
								// DE COMERCIO
								juego.getEstadoJuego().setHaySolicitud(true, juego.
										getPersonajesConectados().get(idEnemigo), MenuInfoPersonaje.
										menuComerciar);
							} else {
								// SI ESTOY DENTRO DE LA ZONA DE BATALLA SETEO QUE SE ABRA EL MENU
								// DE BATALLA
								juego.getEstadoJuego().setHaySolicitud(true,juego.
										getPersonajesConectados().get(idEnemigo), MenuInfoPersonaje.
										menuBatallar);		
							}
							juego.getHandlerMouse().setNuevoClick(false);
						}
					}
				}
			}
		}


		if (juego.getHandlerMouse().getNuevoRecorrido() && !juego.getEstadoJuego().getHaySolicitud()) {

			tileMoverme = Mundo.mouseATile(posMouseRecorrido[0] + juego.getCamara().getxOffset() - xOffset,
					posMouseRecorrido[1] + juego.getCamara().getyOffset() - yOffset);

			juego.getHandlerMouse().setNuevoRecorrido(false);

			pilaMovimiento = null;

			juego.getEstadoJuego().setHaySolicitud(false, null, MenuInfoPersonaje.menuBatallar);
		}

		if (!enMovimiento && tileMoverme != null) {

			enMovimiento = false;

			xInicio = x;
			yInicio = y;

			tileActual = Mundo.mouseATile(x, y);

			if (tileMoverme[0] < 0 || tileMoverme[1] < 0 || tileMoverme[0] >= mundo.obtenerAncho()
					|| tileMoverme[1] >= mundo.obtenerAlto()) {
				enMovimiento = false;
				juego.getHandlerMouse().setNuevoRecorrido(false);
				pilaMovimiento = null;
				tileMoverme = null;
				return;
			}

			if (tileMoverme[0] == tileActual[0] && tileMoverme[1] == tileActual[1]
					|| mundo.getTile(tileMoverme[0], tileMoverme[1]).esSolido()) {
				tileMoverme = null;
				enMovimiento = false;
				juego.getHandlerMouse().setNuevoRecorrido(false);
				pilaMovimiento = null;
				return;
			}

			if (pilaMovimiento == null) {
				pilaMovimiento = caminoMasCorto(tileActual[0], tileActual[1], 
						tileMoverme[0], tileMoverme[1]);
			}
			// Me muevo al primero de la pila
			NodoDePila nodoActualTile = pilaMovimiento.pop();

			if (nodoActualTile == null) {
				enMovimiento = false;
				juego.getHandlerMouse().setNuevoRecorrido(false);
				pilaMovimiento = null;
				tileMoverme = null;
				return;
			}

			tileFinal = new int[2];
			tileFinal[0] = nodoActualTile.obtenerX();
			tileFinal[1] = nodoActualTile.obtenerY();

			xFinal = Mundo.dosDaIso(tileFinal[0], tileFinal[1])[0];
			yFinal = Mundo.dosDaIso(tileFinal[0], tileFinal[1])[1];

			if (tileFinal[0] == tileActual[0] - 1 && tileFinal[1] == tileActual[1] - 1) {
				movimientoHacia = verticalSup;
			}
			if (tileFinal[0] == tileActual[0] + 1 && tileFinal[1] == tileActual[1] + 1) {
				movimientoHacia = verticalInf;
			}
			if (tileFinal[0] == tileActual[0] - 1 && tileFinal[1] == tileActual[1] + 1) {
				movimientoHacia = horizontalIzq;
			}
			if (tileFinal[0] == tileActual[0] + 1 && tileFinal[1] == tileActual[1] - 1) {
				movimientoHacia = horizontalDer;
			}
			if (tileFinal[0] == tileActual[0] - 1 && tileFinal[1] == tileActual[1]) {
				movimientoHacia = diagonalSupIzq;
			}
			if (tileFinal[0] == tileActual[0] + 1 && tileFinal[1] == tileActual[1]) {
				movimientoHacia = diagonalInfDer;
			}
			if (tileFinal[0] == tileActual[0] && tileFinal[1] == tileActual[1] - 1) {
				movimientoHacia = diagonalSupDer;
			}
			if (tileFinal[0] == tileActual[0] && tileFinal[1] == tileActual[1] + 1) {
				movimientoHacia = diagonalInfIzq;
			}
			enMovimiento = true;
		}
	}
	/**Mueve el personaje
	 */
	public void mover() {

		dx = 0;
		dy = 0;

		double paso = 1;

		if (enMovimiento && !(x == xFinal && y == yFinal - 32)) {
			if (movimientoHacia == verticalSup) {
				dy -= paso;
			} else if (movimientoHacia == verticalInf) {
				dy += paso;
			} else if (movimientoHacia == horizontalDer) {
				dx += paso;
			} else if (movimientoHacia == horizontalIzq) {
				dx -= paso;
			} else if (movimientoHacia == diagonalInfDer) {
				dx += paso;
				dy += paso / 2;
			} else if (movimientoHacia == diagonalInfIzq) {
				dx -= paso;
				dy += paso / 2;
			} else if (movimientoHacia == diagonalSupDer) {
				dx += paso;
				dy -= paso / 2;
			} else if (movimientoHacia == diagonalSupIzq) {
				dx -= paso;
				dy -= paso / 2;
			}

			x += dx;
			y += dy;

			// Le envio la posicion
			if (intervaloEnvio == 2) {
				enviarPosicion();
				intervaloEnvio = 0;
			}
			intervaloEnvio++;

			if (x == xFinal && y == yFinal - 32) {
				enMovimiento = false;
			}
		}
	}
	/**Grafica el frame del personaje
	 */
	public void graficar(final Graphics g) {
	    drawX = (int) (x - juego.getCamara().getxOffset());
	    drawY = (int) (y - juego.getCamara().getyOffset());
	    g.drawImage(getFrameAnimacionActual(), drawX, drawY + 4, ancho, alto, null);
	}
	/**Grafica el nombre
	 */
	public void graficarNombre(final Graphics g) {
		g.setColor(Color.WHITE);
		g.setFont(new Font("Book Antiqua", Font.BOLD, 15));
	    Pantalla.centerString(g, new java.awt.Rectangle(drawX + 32, drawY - 20, 0, 10), nombre);
	}
	/**Obtiene el frameActual del personaje
	 */
	private BufferedImage getFrameAnimacionActual() {
		if (movimientoHacia == horizontalIzq) {
			return moverIzq.getFrameActual();
		} else if (movimientoHacia == horizontalDer) {
			return moverDer.getFrameActual();
		} else if (movimientoHacia == verticalSup) {
			return moverArriba.getFrameActual();
		} else if (movimientoHacia == verticalInf) {
			return moverAbajo.getFrameActual();
		} else if (movimientoHacia == diagonalInfIzq) {
			return moverAbajoIzq.getFrameActual();
		} else if (movimientoHacia == diagonalInfDer) {
			return moverAbajoDer.getFrameActual();
		} else if (movimientoHacia == diagonalSupIzq) {
			return moverArribaIzq.getFrameActual();
		} else if (movimientoHacia == diagonalSupDer) {
			return moverArribaDer.getFrameActual();
		}

		return Recursos.orco.get(6)[0];
	}
	/**Pide la direccion donde va
	 * @return devuelve el movimiento hacia donde va
	 */
	private int getDireccion() {
		return movimientoHacia;
	}
	/**Obtiene el frame donde esta el personaje
	 */
	private int getFrame() {
		if (movimientoHacia == horizontalIzq) {
			return moverIzq.getFrame();
		} else if (movimientoHacia == horizontalDer) {
			return moverDer.getFrame();
		} else if (movimientoHacia == verticalSup) {
			return moverArriba.getFrame();
		} else if (movimientoHacia == verticalInf) {
			return moverAbajo.getFrame();
		} else if (movimientoHacia == diagonalInfIzq) {
			return moverAbajoIzq.getFrame();
		} else if (movimientoHacia == diagonalInfDer) {
			return moverAbajoDer.getFrame();
		} else if (movimientoHacia == diagonalSupIzq) {
			return moverArribaIzq.getFrame();
		} else if (movimientoHacia == diagonalSupDer) {
			return moverArribaDer.getFrame();
		}

		return 0;
	}
	/**Envia la posicion del personaje
	 */
	private void enviarPosicion() {
		juego.getUbicacionPersonaje().setPosX(x);
		juego.getUbicacionPersonaje().setPosY(y);
		juego.getUbicacionPersonaje().setDireccion(getDireccion());
		juego.getUbicacionPersonaje().setFrame(getFrame());
		try {
			juego.getCliente().getSalida().writeObject(gson.toJson(juego.
					getUbicacionPersonaje(), PaqueteMovimiento.class));
		} catch (IOException e) {
			JOptionPane.showMessageDialog(null, "Fallo la conexión con el servidor");
		}
	}
	/**Busca el camino más corto a recorrer para llegar a una posición
	 * @param xInicial ubicacion en X inicial
	 * @param yInicial ubicacion en Y inicial
	 * @param xFinal ubicacion en X final
	 * @param yFinal ubicacion en Y final
	 * @return la pila de tiles a recorrer
	 */
	private PilaDeTiles caminoMasCorto(final int xInicial, final int yInicial, final int xFinal, final int yFinal) {
		Grafo grafoLibres = mundo.obtenerGrafoDeTilesNoSolidos();
		// Transformo las coordenadas iniciales y finales en indices
		int nodoInicial = (yInicial - grafoLibres.obtenerNodos()[0].obtenerY())
				* (int) Math.sqrt(grafoLibres.obtenerCantidadDeNodosTotal()) + xInicial
				- grafoLibres.obtenerNodos()[0].obtenerX();

		int nodoFinal = (yFinal - grafoLibres.obtenerNodos()[0].obtenerY())
				* (int) Math.sqrt(grafoLibres.obtenerCantidadDeNodosTotal()) + xFinal
				- grafoLibres.obtenerNodos()[0].obtenerX();

		// Hago todo
		double[] vecCostos = new double[grafoLibres.obtenerCantidadDeNodosTotal()];
		int[] vecPredecesores = new int[grafoLibres.obtenerCantidadDeNodosTotal()];
		boolean[] conjSolucion = new boolean[grafoLibres.obtenerCantidadDeNodosTotal()];
		int cantSolucion = 0;
		// Lleno la matriz de costos de numeros grandes
		for (int i = 0; i < grafoLibres.obtenerCantidadDeNodosTotal(); i++) {
			vecCostos[i] = Double.MAX_VALUE;
		}
		// Adyacentes al nodo inicial
		conjSolucion[nodoInicial] = true;
		cantSolucion++;
		vecCostos[nodoInicial] = 0;
		Nodo[] adyacentes = grafoLibres.obtenerNodos()[nodoInicial].obtenerNodosAdyacentes();
		for (int i = 0; i < grafoLibres.obtenerNodos()[nodoInicial].obtenerCantidadDeAdyacentes(); i++) {
			if (estanEnDiagonal(grafoLibres.obtenerNodos()[nodoInicial],
					grafoLibres.obtenerNodos()[adyacentes[i].obtenerIndice()])) {
				vecCostos[adyacentes[i].obtenerIndice()] = 1.5;
			} else {
				vecCostos[adyacentes[i].obtenerIndice()] = 1;
			}
			vecPredecesores[adyacentes[i].obtenerIndice()] = nodoInicial;
		}
		// Aplico Dijkstra
		while (cantSolucion < grafoLibres.obtenerCantidadDeNodosTotal()) {
			// Elijo W perteneciente al conjunto restante tal que el costo de W
			// sea minimo
			double minimo = Double.MAX_VALUE;
			int indiceMinimo = 0;
			Nodo nodoW = null;
			for (int i = 0; i < grafoLibres.obtenerCantidadDeNodosTotal(); i++) {
				if (!conjSolucion[i] && vecCostos[i] < minimo) {
					nodoW = grafoLibres.obtenerNodos()[i];
					minimo = vecCostos[i];
					indiceMinimo = i;
				}
			}
			// Pongo a W en el conj solucion
			conjSolucion[indiceMinimo] = true;
			cantSolucion++;
			// Por cada nodo I adyacente a W del conj restante
			// Le sumo 1 al costo de ir hasta W y luego ir hasta su adyacente
			adyacentes = grafoLibres.obtenerNodos()[indiceMinimo].obtenerNodosAdyacentes();
			for (int i = 0; i < grafoLibres.obtenerNodos()[indiceMinimo].obtenerCantidadDeAdyacentes(); i++) {
				double valorASumar = 1;
				if (estanEnDiagonal(grafoLibres.obtenerNodos()[indiceMinimo],
						grafoLibres.obtenerNodos()[adyacentes[i].obtenerIndice()])) {
					valorASumar = 1.5;
				}
				if (vecCostos[indiceMinimo] + valorASumar < vecCostos[adyacentes[i].obtenerIndice()]) {
					vecCostos[adyacentes[i].obtenerIndice()] = vecCostos[indiceMinimo] + valorASumar;
					vecPredecesores[adyacentes[i].obtenerIndice()] = indiceMinimo;
				}
			}
		}
		// Creo el vector de nodos hasta donde quiere llegar
		PilaDeTiles camino = new PilaDeTiles();
		while (nodoFinal != nodoInicial) {
			camino.push(new NodoDePila(grafoLibres.obtenerNodos()[nodoFinal].obtenerX(),
					grafoLibres.obtenerNodos()[nodoFinal].obtenerY()));
			nodoFinal = vecPredecesores[nodoFinal];
		}

		return camino;
	}
	/**Pregunta si los personajes estan en diagonal
	 * @param nodoUno personaje 1
	 * @param nodoDos personaje 2
	 * @return true or false
	 */
	private boolean estanEnDiagonal(final Nodo nodoUno, final Nodo nodoDos) {
		if (nodoUno.obtenerX() == nodoDos.obtenerX() || nodoUno.obtenerY() == nodoDos.obtenerY())
			return false;
		return true;
	}
	/**Pide el valor de X 
	 * @return devuelve la ubicacion en X
	 */
	public float getX() {
		return x;
	}
	/**Setea el valor de X
	 * @param x valor nuevo de la ubicacion en X
	 */
	public void setX(final float x) {
		this.x = x;
	}
	/**Pide el valor de Y 
	 * @return devuelve la ubicacion en Y
	 */
	public float getY() {
		return y;
	}
	/**Setea el valor de Y
	 * @param y valor nuevo de la ubicacion en Y
	 */
	public void setY(final float y) {
		this.y = y;
	}
	/**Pide el ancho 
	 * @return devuelve el ancho
	 */
	public int getAncho() {
		return ancho;
	}
	/**Setea el ancho
	 * @param ancho nuevo ancho a setear
	 */
	public void setAncho(final int ancho) {
		this.ancho = ancho;
	}
	/**Pide el alto 
	 * @return devuelve el alto
	 */
	public int getAlto() {
		return alto;
	}
	/**Setea el alto
	 * @param alto nuevo alto a setear
	 */
	public void setAlto(final int alto) {
		this.alto = alto;
	}
	/**Pide el offset de X
	 * @return devuelve el offset de X
	 */
	public int getxOffset() {
		return xOffset;
	}
	/**Pide el offset de Y 
	 * @return devuelve el offset de Y
	 */
	public int getYOffset() {
		return yOffset;
	}
}
