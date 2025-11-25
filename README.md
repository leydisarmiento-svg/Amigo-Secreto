<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Juego de Amigo Secreto - Acceso Único</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f4f4f9;
            color: #333;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
            padding: 20px;
        }
        .container {
            background: #ffffff;
            padding: 30px;
            border-radius: 10px;
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
            max-width: 450px;
            width: 100%;
            text-align: center;
        }
        h2 {
            color: #d35400; /* Naranja festivo */
        }
        input[type="text"], button {
            width: 100%;
            padding: 10px;
            margin: 8px 0;
            display: inline-block;
            border: 1px solid #ccc;
            border-radius: 4px;
            box-sizing: border-box;
        }
        button {
            background-color: #e74c3c; /* Rojo festivo */
            color: white;
            padding: 14px 20px;
            margin: 10px 0;
            border: none;
            cursor: pointer;
            font-size: 16px;
            font-weight: bold;
            transition: background-color 0.3s;
        }
        button:hover {
            background-color: #c0392b;
        }
        #resultado {
            margin-top: 20px;
            padding: 15px;
            border: 2px dashed #d35400;
            border-radius: 8px;
            text-align: left;
        }
        .hidden {
            display: none;
        }
        
        #lista-deseos img {
            max-width: 100%;
            height: auto;
            display: block;
            margin: 10px 0;
            border-radius: 4px;
        }
    </style>
</head>
<body>

<div class="container">
    <h2>✨ Portal de Amigo Secreto ✨</h2>

    <div id="modulo-ingreso">
        <p>Ingresa tu nombre y código secreto para ver tu asignación.</p>
        <input type="text" id="nombre-input" placeholder="Tu Nombre">
        <input type="text" id="codigo-input" placeholder="Tu Código Secreto">
        <button onclick="accederAlJuego()">ACCEDER</button>
    </div>

    <div id="resultado" class="hidden">
        <h3>🎉 ¡Tu Amigo Secreto es... <span id="nombre-receptor"></span>! 🎉</h3>
        <p>⚠️ **Recuerda bien el nombre y los deseos. Esta información es de ACCESO ÚNICO.**</p>
        <hr>
        <h4>🎁 Opciones de Regalo:</h4>
        <ul id="lista-deseos">
            </ul>
        <button onclick="cerrarSesion()">CERRAR Y BLOQUEAR ACCESO</button>
    </div>

    <div id="modulo-bloqueo" class="hidden">
        <h3>🚫 Acceso Bloqueado 🚫</h3>
        <p>Ya has abierto y visualizado a tu Amigo Secreto. ¡Es hora de comprar el regalo!</p>
    </div>
</div>

<script>
    
    
    const PARTICIPANTES = [
        { nombre: "HEIDY GARCIA", codigo: "heidy.garcia@linktic.com", id_receptor: 3, deseos: ["MAQUILLAJE (RUBOR,DELINEADOR,CORRECTOR,ENTRE OTROS)", "CHAQUETAS", "ACCESORIOS COMO ( ARETES, RELOJES, PULSERAS, ARETES, ENTRE OTROS)"] },
        { nombre: "ANDRES VERA", codigo: "andres.vera@linktic.com", id_receptor: 4, deseos: ["BONO KOAJ", "PERFUME AMADERADO", "RELOJ"] },
        { nombre: "LEYDI SARMIENTO", codigo: "leydi.sarmiento@linktic.com", id_receptor: 5, deseos: ["MALETA TOTTO", "BOTELLA DE OLD PARD 12 AÑOS", "AUDIFONOS SAMSUNG BUDS FAN)"] },
        { nombre: "ANDRES OVIEDO", codigo: "carlos.oviedo@linktic.com", id_receptor: 3, deseos: ["MORRAL PC"] },
        { nombre: "ANDRES OVALLE", codigo: "andres.ovalle@linktic.com", id_receptor: 4, deseos: ["CAMISAS", "Whiskey Jack Daniel's tenessi firé", "Bono en ropa o comida"] },
        { nombre: "JHONATTAN YOPASA", codigo: "jhonattan.yopasa@linktic.com", id_receptor: 3, deseos: ["Reloj Q&Q / Bono para reloj", "Bono fallabela", "Billetera Cuero / Bono para Billetera"] },
        { nombre: "NURY AVILA", codigo: "nury.avila@linktic.com", id_receptor: 4, deseos: ["Skincare de trendy", "bolso de oficina lindo, negro o cafe", "ropa deportiva y guantes JAJAJA"] },
        { nombre: "NATALY SANCHEZ", codigo: "nataly.sanchez@linktic.com", id_receptor: 3, deseos: ["Secador de Cabello.", "Kit de Maquillaje bloomshell ( Base Liquida Cushion tono 04) lo de mas si lo dejo a elección, puede ser gloss, sombras(Tono tierra) labial."] },
        { nombre: "MARIA LEON", codigo: "maria.leon@linktic.com", id_receptor: 4, deseos: ["Rodilleras y mangas de voleibol de decathlon", "Un kit completico de maquillaje de trendy, (sobre todo iluminador,rubor, pestañina, labial color mate,encrespador)y el resto lo que Diosito ponga en tu corazón jeje", "Un bono de ropa, específicamente de yass moda"] },
        { nombre: "SANDY CAMELO", codigo: "sandy.camelo@linktic.com", id_receptor: 3, deseos: ["Joodye Monster of Rock Talla M https://www.dissey.com.co/", "BONO FALABELLA O STUDIO F", "Jakc daniels honey 1 UND"] },
        { nombre: "GLORIA MENDOZA", codigo: "gloria.mendoza@linktic.com", id_receptor: 4, deseos: ["Organizadores" <img src='c:\Users\UsuarioP\Downloads\Screenshot_20251116_205612_Chrome - Carlos Mauricio Oviedo Pinto.jpg' alt='Imagen de Morral para PC' /> "Humificador", "Chaqueta talla M / https://www.facebook.com/share/1BoQzVUxto/"] },
        { nombre: "YINETH MONSALVE", codigo: "yineth.monsalve@linktic.com", id_receptor: 3, deseos: ["Libros: | Deja de ser tú - Joe Dispenza | El placebo eres tú – Joe Dispenza | Sobrenatural – Joe Dispenza | Sicología de la Metafísica – Conny Méndez | El librito azul - Conny Méndez | Hábitos Atómicos – James Clear | Piense y hágase rico – Napoleon Hill | Pide y se te dará – Esther y Jerry Hicks (Abraham Hicks) | El kybalión – Tres iniciados | Los cuatro acuerdos – Don Miguel Ruiz | En este perfil, @tulibreriacol, venden 7 libros por 100mil. O bueno, donde quieran, me dejo sorprender con la selección.", "Bolso tipo cartera, amplio, casual. En colores, negro, café, gris, rosado o vinotinto.", "Conjunto de accesorio de plata de corazón como la imagen de referencia. De preferencia el del enlace adjunto. | https://calipsojoyeria.com/product/conjunto-veneciana-corazon-corona/?utm_source=ig&utm_medium=social&utm_content=link_in_bio&fbclid=PAZnRzaAOMbvZleHRuA2FlbQIxMQBzcnRjBmFwcF9pZA8xMjQwMjQ1NzQyODc0MTQAAac4j_uHNMFdnkRjUyu3UgWeAtcOb93JqQubn1wZ1U-ZcpY7gOmpGBZ8A-OARQ_aem_DfZKyGew_qb9Yw4QsUqc3Q"] },
        { nombre: "CAROL GARCIA", codigo: "carol.garcia@linktic.com", id_receptor: 1, deseos: ["AirPods Max – Gris espacial 🎧 Sería un regalo increíble y muy útil para mi día a día. Si eliges los AirPods Max, puedes añadir también unos audífonos pequeños para seguir intercambiando mis outfits en el gym y combinarlos según el día 🏋️‍♀️🎧✨", "La siguiente opción de regalo que me encantaría un enterizo de Cervo Sport. En este <a href='https://www.instagram.com/cervo_sport' target='_blank'>enlace</a> puedes ver todas las opciones y si te animas también puedes escoger otros conjuntos que te gusten: 👉 <a href='https://www.instagram.com/cervo_sport?igsh=MXZocjN4aDM4d3cxbw==' target='_blank'>@cervo_sport</a> 📍 La marca es de Ibagué, se demoran aproximadamente 3 días en el envío y reciben varios medios de pago. Te dejo una imagen de referencia para que veas más o menos el estilo del enterizo que me gusta: un color vivo, que vaya bien con mi tono de piel. 📏 Talla M Si tienes alguna duda, no dudes en dejarme un mensaje. 😊💛 ¡Gracias desde ya por tu detalle!", "Bono de regalo de Hair Fit Salon 💇‍♀️✨ Es un excelente lugar para el cuidado del cabello. Aquí puedes verlos 👉 <a href='https://www.instagram.com/hairfitsalon?igsh=MWw3ZXJ6dG92ejd2dw==' target='_blank'>@hairfitsalon</a> Aceptan varios medios de pago y su servicio es espectacular. Otra opción es un kit de “La Poción” para salud capilar, que puedes encontrar en varios puntos de Bogotá. Te dejo una imagen de referencia del kit. Si optas por esta alternativa, me puedes escribir para escoger juntos el kit perfecto."] }
    ];



    function accederAlJuego() {
        const nombreInput = document.getElementById('nombre-input').value.trim();
        const codigoInput = document.getElementById('codigo-input').value.trim();
        const moduloIngreso = document.getElementById('modulo-ingreso');
        const resultadoDiv = document.getElementById('resultado');
        const bloqueoDiv = document.getElementById('modulo-bloqueo');

       
        const regalador = PARTICIPANTES.find(p => p.nombre.toLowerCase() === nombreInput.toLowerCase() && p.codigo === codigoInput);

        if (!regalador) {
            alert("Credenciales incorrectas. Verifica tu nombre y código.");
            return;
        }

        const claveBloqueo = `visto_${regalador.nombre.toLowerCase()}`;
        
        
        if (localStorage.getItem(claveBloqueo) === 'SÍ') {
            moduloIngreso.classList.add('hidden');
            bloqueoDiv.classList.remove('hidden');
            return;
        }

        
        const receptor = PARTICIPANTES.find(p => p.id_receptor === regalador.id_receptor);

    
        document.getElementById('nombre-receptor').textContent = receptor.nombre;
        const listaDeseos = document.getElementById('lista-deseos');
        listaDeseos.innerHTML = ''; 
        receptor.deseos.forEach(deseo => {
            const li = document.createElement('li');
            
           
            li.innerHTML = deseo;
            
            listaDeseos.appendChild(li);
        });

        
        moduloIngreso.classList.add('hidden');
        resultadoDiv.classList.remove('hidden');
    }

    function cerrarSesion() {
        const nombreInput = document.getElementById('nombre-input').value.trim();
        const regalador = PARTICIPANTES.find(p => p.nombre.toLowerCase() === nombreInput.toLowerCase());

        if (regalador) {
            
            const claveBloqueo = `visto_${regalador.nombre.toLowerCase()}`;
            localStorage.setItem(claveBloqueo, 'SÍ');
        }
        
        
        window.location.reload(); 
    }
</script>

</body>
</html># Amigo-Secreto
