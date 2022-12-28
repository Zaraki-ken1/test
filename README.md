## Imagenes
![enter image description here](https://i.ibb.co/j6rM5yn/board.jpg)
![enter image description here](https://i.ibb.co/02CLZmb/top1.jpg)
![](https://i.ibb.co/3mRdFR5/top2.jpg)
![enter image description here](https://i.ibb.co/F7cPGkB/top3.jpg)
## Características

 - Cada mazmorra agregada al sistema deberá contener la siguiente información.
	 - map_index:
		 - El número del mapa es utilizado como clave primaria, además también es utilizado para cargar las imágenes ingame. .
			 - `"d:/ymir work/ui/dungeon_info/%s_ico.tga" % map_index`
			 - `"d:/ymir work/ui/dungeon_info/%s_bg.tga" % file_name`
		 - Acciones que se realizan cuando el jefe de la mazmorra recibe daño de un jugador:
			 - **Este comportamiento solo esta disponible en mapas privados.**
			 - Solo se registran el daño más alto que el jugador le ella realizado al jefe de la mazmorra.
		 - Acciones que se realizan cuando el jefe de la mazmorra es derrotado.
			 - **Este comportamiento solo esta disponible en mapas privados.**
			 - Cuando el jefe de la mazmorra sea derrotado los usuarios en el mapa privado deberán esperar 10s(normalmente son 10) para que toda la información de la mazmorra sea guardada.
				 - Solo se guarda la información de los jugadores que se encuentren hasta el final, si un jugador salio/desconecto durante la mazmorra este no será tomado en cuenta.
		 - Acciones que se realizan cuando un mob es derrotado (Usado en mazmorras como la **Torre del Demonio**) se tomara como ejemplo la torre.
			 - **Este comportamiento solo esta disponible en el mapa base**
			 - Cuando el Metin de Avaria sea derrotado, el tiempo de espera será el tiempo que este especificado en el **regen.txt** para que vuelva a aparecer el mob.
				 - Por lo general esté tiempo va a salir < al tiempo especificado en el **regen.txt** debido a que se calcula desde la ultima comprobación de reaparecer.
	 - dungeon_name:
		 - El nombre de la mazmorra.
	 - access_type:
		 - El tipo de acceso requerido por la mazmorra.
			 - Solitario.
			 - Grupo.
			 - Solitario / Grupo.
			 - Gremio.
	 - level_limit:
		 - El nivel mínimo requerido para acceder a la mazmorra.
	 - party_members:
		 - Mínimo de miembros requeridos en grupo para acceder a la mazmorra.
	 - cooldown_type:
		 - El tipo de tiempo de espera (Se mostrara el valor más alto).
			 - Todos los canales.
			 - Canal.
				 - Estos 2 tiempos son los que todos los jugadores deberán esperar para volver a realizar la mazmorra.
			 - Personal.
				 - Este tiempo es el que el jugador deberá esperar para volver a realizar la mazmorra.
	 - personal_cooldown:
		 - El tiempo de espera personal en segundos.
			 - cooldown_type: channel / all_channels.
	 - dungeon_cooldown:
		 - El tiempo de espera de la mazmorra en segundos.
			 - cooldown_type: personal.
	 - duration:
		 - La duración de la mazmorra.
	 - entrance:
		 - El lugar al que el jugador se deberá dirigir para acceder a la mazmorra.
	 - strength_bonus:
		 - La **Fuerza contra x bonus**.
	 - resistance_bonus:
		 - La **Resistencia x bonus**.
	 - mob_vnum:
		 - Es el vnum que se requiere para iniciar la mazmorra (Ej. **Torre del Demonio**).
	 - boss_vnum:
		 - El el vnum de la mazmorra, este será utilizado para saber cuando guardar la información.
	 - item_vnum:
		 - Es el vnum del objeto que se requiere para acceder a la mazmorra.
	 - item_count:
		 - La cantidad del objetos que se requiere para acceder a la mazmorra.
	 - map_x:
		 - Las coordenadas en x del mapa a donde es teletransportado el jugador.
	 - map_y:
		 - Las coordenadas en y del mapa a donde es teletransportado el jugador.
 - Para las mazmorra que no inician cuando el jugador derrote a un mob se requiere previa configuración en el .lua / .quest.
	 - Funciones a agregar:
		 - pc.start_personal_cooldown(**map_index**).
		 - game.start_dungeon_cooldown(**map_index**).
	 - El fin de la mazmora no es necesario configurarlo ya que se tomara como final cuando el jefe sea derrotado.

## Finalizar mazmorra
Cuando el jugador o jugadores completen la mazmorra se guardará la siugiente información.

 - Tiempo de Mazmrra (el tiempo que duraron dentro de la mazmorra hasta completarla).
 - El daño efecutado al jefe (es individual por cada jugador).
 - El numero de veces que el jugador ha completado la mazmorra se suma un punto.

## Información técnica.

 - Las consultas SQL se se ejecutan cuando el servidor inicia o se requiere alguna actualización o inserción de datos. El resto de la información se guarda en memoria. Por cada actualización a la información de cada jugador en determinada mazmorra se utiliza el sistema P2P para enviar los nuevos datos a los demás canales.
 - También se utiliza el sistema P2P para enviar la información sobre el tiempo de espera de cada mazmorra
 - El sistema cuenta con una característica que viene por defecto desactivada que permite tener en tiempo real el tiempo de espera.
	 - Está característica envía cada segundo un paquete de datos a los usuarios con la información.

## NOTA

 - Es posible que experimente un pequeño retardo de 1 segundo en el tiempo de espera, esto se debe a su conexión de internet debido al tiempo en que tarda el paquete de datos en ser recibido por el cliente y comenzar la cuenta regresiva.
 - Tenga en cuenta que este sistema no incluye **de momento** el control de tiempo de espera en los .lua, se sigue utilizando el sistema tradicional (es posible que lo siga siendo así).

## Historial de Actualizaciones
 - Versión 0.2:
	- Mejoras en el código encargado del cooldown.
		- DEAD:
			- Cuando el el vnum especificado en mob_vnum sea derrotado y si tiene activado este flag se inicia el cooldown.
				- Personal se tomara de lo especificado en la configuración SQL. Para channel y all channels se usara el tiempo de regen.txt
		- PRIVATE_MAP_INDEX:
			- Cuando se crea un mapa privado (dungeon.cpp) se inicia el cooldown para channel y all channels.
		- MANUAL:
			- Este flag funciona solo desde las funciones de lua especificadas antes.
	- Regen.txt:
		- Para la mazmorra de Torre de demonio en ocasiones el contador aun estaba en proceso y el metin aparecía, ahora el contador es puesto en disponible si el metin aparece.
	- Solucionado los errores al compilar en server y cliente con versiones del source que no son de marty sama.
	- Solucionado el texto de sincronizar se queda pegado a la espera de una respuesta.
		- Nunca se envía si el jugador no cumple el requisito del nivel.
- Versión 0.3:
	- Solucionado; La información del ranking no se mostraba si solo un jugador se encontraba registrado.
	- El listado de mazmorra ahora se muestra en orden alfabético.
	- Ahora se requiere el registro de nivel mínimo y máximo
