# Crear el nuevo HTML con diseño estilo columna por semestre como en la imagen (más visual y elegante)

# Definir los semestres manualmente (distribución estimada basada en años, 2 semestres por año)
semestres = {
    1: ["Matemática general", "Biología celular", "Introducción a la med vet", "Taller de comprensión oral y escrita", "Química"],
    2: ["Histoembriología", "Anatomía del Canino", "Bioquímica", "Inglés I", "Bioestadística"],
    3: ["Práctica básica", "Inglés II", "Zoología", "Medio Ambiente y Gestión Ambiental", "Anatomía Comparada"],
    4: ["Microbiología", "Fisiología Animal", "Enfermedades Parasitarias", "Administración y Emprendimiento Veterinario", "Genética"],
    5: ["Tecnología de los Alimentos", "Fisiopatología", "Etología y Bienestar Animal", "Reproducción e Inseminación Artificial", "Inmunología", "Nutrición y Alimentación Animal"],
    6: ["Patología de Sistemas", "Farmacología y Toxicología", "Obstetricia y Ginecología", "Enfermedades Infecciosas", "Control de Calidad de los Alimentos", "Producción Avícola"],
    7: ["Epidemiología Veterinaria", "Práctica Intermedia", "Laboratorio Clínico", "Producción Ovinos y Caprinos", "Producción Porcina", "Semiología"],
    8: ["Medicina de Felinos", "Cirugía General", "Medicina de Animales Exóticos", "Medicina de Caninos", "Medicina de Animales Mayores"],
    9: ["Producción Bovinos de Carne y Leche", "Metodología de la Investigación", "Producción Acuícola", "Patología Quirúrgica", "Formulación y Evaluación de Proyectos Agropecuarios", "Práctica Final", "Diagnóstico por Imágenes"],
    10: ["Salud Pública", "Clínica de Animales Menores", "Clínica de Animales Mayores", "Farmacología Aplicada", "Trabajo de Titulación"]
}

# Iniciar HTML con estilo visual estilo columnas
html_head = """
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>Malla Veterinaria UDLA</title>
  <style>
    body {
      font-family: 'Segoe UI', sans-serif;
      background-color: #fff0f5;
      margin: 0;
      padding: 20px;
    }
    h1 {
      text-align: center;
      color: #880e4f;
    }
    .contenedor {
      display: flex;
      flex-wrap: nowrap;
      overflow-x: auto;
      gap: 20px;
      padding: 10px;
    }
    .semestre {
      background-color: #fce4ec;
      border-radius: 15px;
      padding: 15px;
      min-width: 230px;
      flex: 0 0 auto;
      box-shadow: 0 2px 6px rgba(0,0,0,0.1);
    }
    .semestre h2 {
      text-align: center;
      color: #ad1457;
      margin-top: 0;
    }
    .ramo {
      background-color: #f8bbd0;
      margin: 8px 0;
      padding: 10px;
      border-radius: 10px;
      text-align: center;
      font-weight: bold;
      color: #4a004e;
      cursor: pointer;
      position: relative;
      transition: all 0.3s ease;
    }
    .ramo:hover::after {
      content: attr(data-descripcion);
      position: absolute;
      background: #6a1b9a;
      color: white;
      padding: 6px 10px;
      border-radius: 6px;
      top: 100%;
      left: 50%;
      transform: translateX(-50%);
      white-space: nowrap;
      z-index: 10;
      font-size: 13px;
      font-weight: normal;
    }
    .ramo.bloqueado {
      background-color: #e1bee7;
      color: #7b1fa2;
      opacity: 0.6;
      cursor: not-allowed;
    }
    .ramo.aprobado {
      background-color: #c8e6c9;
      color: #1b5e20;
    }
  </style>
</head>
<body>
<h1>Malla Medicina Veterinaria - UDLA</h1>
<div class="contenedor">
"""

html_script = """
</div>
<script>
  const ramos = document.querySelectorAll('.ramo');
  const estado = JSON.parse(localStorage.getItem('estadoRamos') || '{}');

  function actualizarEstadoVisual() {
    ramos.forEach(ramo => {
      const id = ramo.id;
      const prerreqs = ramo.dataset.prerrequisitos.split(',').filter(p => p);
      const aprobados = prerreqs.every(p => estado[p]);
      if (!aprobados && !estado[id]) {
        ramo.classList.add('bloqueado');
      } else {
        ramo.classList.remove('bloqueado');
      }

      if (estado[id]) {
        ramo.classList.add('aprobado');
      } else {
        ramo.classList.remove('aprobado');
      }
    });
  }

  ramos.forEach(ramo => {
    ramo.addEventListener('click', () => {
      const id = ramo.id;
      if (ramo.classList.contains('bloqueado')) return;
      estado[id] = !estado[id];
      localStorage.setItem('estadoRamos', JSON.stringify(estado));
      actualizarEstadoVisual();
    });
  });

  actualizarEstadoVisual();
</script>
</body>
</html>
"""

# Crear columnas por semestre
html_columnas = ""
for n_semestre, lista in semestres.items():
    columna = f'<div class="semestre"><h2>{n_semestre}° Semestre</h2>\n'
    for ramo in lista:
        ramo_id = ramo.lower().replace(" ", "_")
        # Buscar si tiene prerrequisitos
        prereq = prerrequisitos.get(ramo, [])
        prereq_ids = [p.lower().replace(" ", "_") for p in prereq]
        descripcion = f"Prerrequisitos: {', '.join(prereq) if prereq else 'Ninguno'}"
        columna += f'<div class="ramo" id="{ramo_id}" data-prerrequisitos="{",".join(prereq_ids)}" data-descripcion="{descripcion}">{ramo}</div>\n'
    columna += '</div>'
    html_columnas += columna

# Unir todo y guardar
html_final = html_head + html_columnas + html_script
ruta_final = "/mnt/data/Malla veterinaria UDLA - Colores Bonitos.html"
with open(ruta_final, "w", encoding="utf-8") as f:
    f.write(html_final)

ruta_final
