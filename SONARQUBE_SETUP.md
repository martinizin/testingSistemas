# Guía de Configuración de SonarQube para Sistema A Frontend

## Prerrequisitos
- Node.js instalado
- Repositorio Git
- Cuenta de SonarQube (SonarCloud) o servidor local de SonarQube

## Paso 1: Instalar Dependencias
```bash
npm install
```

## Paso 2: Elegir tu Configuración de SonarQube

### Opción A: SonarCloud (Recomendado)
1. Ve a [SonarCloud](https://sonarcloud.io/)
2. Inicia sesión con tu cuenta de GitHub/GitLab
3. Crea un nuevo proyecto y selecciona tu repositorio
4. Obtén tu clave de proyecto y clave de organización
5. Genera un token desde la configuración de tu cuenta de SonarCloud

### Opción B: Servidor Local de SonarQube
1. Descarga SonarQube desde el [sitio web oficial](https://www.sonarqube.org/downloads/)
2. Extrae y ejecuta: `bin/[OS]/sonar.sh start`
3. Accede a http://localhost:9000 (admin/admin)
4. Crea un nuevo proyecto y obtén la clave del proyecto
5. Genera un token

## Paso 3: Configurar Autenticación

### Para SonarCloud:
Crea un archivo `.env` en la raíz de tu proyecto:
```
SONAR_TOKEN=tu_token_de_sonarcloud_aqui
SONAR_ORGANIZATION=tu_clave_de_organizacion
SONAR_HOST_URL=https://sonarcloud.io
```

### Para SonarQube Local:
Crea un archivo `.env` en la raíz de tu proyecto:
```
SONAR_TOKEN=tu_token_local_aqui
SONAR_HOST_URL=http://localhost:9000
```

## Paso 4: Actualizar sonar-project.properties
Edita el archivo `sonar-project.properties` con tus valores específicos:
```properties
# Actualiza estos valores:
sonar.projectKey=tu-clave-de-proyecto
sonar.organization=tu-clave-de-organizacion (solo para SonarCloud)
```

## Paso 5: Ejecutar tu Primer Análisis

### Generar Reporte de ESLint y Ejecutar SonarQube:
```bash
npm run analyze
```

### O ejecutar pasos por separado:
```bash
# Generar reporte de ESLint
npm run lint:report

# Ejecutar análisis de SonarQube
npm run sonar
```

### Para SonarQube local:
```bash
npm run sonar:local
```

## Paso 6: Ver Resultados
- **SonarCloud**: Revisa el dashboard de tu proyecto en sonarcloud.io
- **SonarQube Local**: Ve a http://localhost:9000

## Paso 7: Integrar con CI/CD (Opcional)

### Ejemplo de GitHub Actions:
Crea `.github/workflows/sonar.yml`:
```yaml
name: Análisis de SonarQube
on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  sonarqube:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
      with:
        fetch-depth: 0
    
    - name: Configurar Node.js
      uses: actions/setup-node@v2
      with:
        node-version: '18'
    
    - name: Instalar dependencias
      run: npm ci
    
    - name: Ejecutar ESLint
      run: npm run lint:report
    
    - name: Escaneo de SonarQube
      uses: sonarqube-quality-gate-action@master
      env:
        SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
```

## Entendiendo los Reportes de SonarQube

### Métricas Clave:
- **Bugs**: Posibles errores de ejecución
- **Vulnerabilidades**: Problemas de seguridad
- **Code Smells**: Problemas de mantenibilidad
- **Duplicaciones**: Bloques de código repetidos
- **Cobertura**: Porcentaje de cobertura de pruebas

### Quality Gates (Puertas de Calidad):
- Define criterios de calidad para tu proyecto
- Fallan las compilaciones si no se cumplen los estándares de calidad
- Umbrales personalizables

## Problemas Comunes y Soluciones

### Problema: "sonar-scanner command not found"
**Solución**: Asegúrate de que sonar-scanner esté instalado globalmente:
```bash
npm install -g sonar-scanner
```

### Problema: Fallo de autenticación
**Solución**: 
1. Verifica que tu token sea correcto
2. Asegúrate de que el archivo .env esté en la raíz del proyecto
3. Verifica que sonar-project.properties tenga las claves correctas

### Problema: No hay resultados de análisis
**Solución**:
1. Verifica si los archivos fuente están en la ubicación correcta (src/)
2. Verifica que las exclusiones no excluyan todos los archivos
3. Verifica que el servidor de SonarQube esté ejecutándose (para configuración local)

## Mejores Prácticas

1. **Ejecutar análisis regularmente**: Integra con tu flujo de trabajo de desarrollo
2. **Corregir problemas prontamente**: Aborda bugs y vulnerabilidades rápidamente
3. **Monitorear tendencias**: Rastrea métricas de calidad a lo largo del tiempo
4. **Establecer puertas de calidad**: Define y aplica estándares de calidad
5. **Revisar reportes**: Revisa regularmente los dashboards de SonarQube

## Próximos Pasos

1. Configurar análisis automatizado en tu pipeline de CI/CD
2. Configurar puertas de calidad para tu proyecto
3. Agregar pruebas unitarias y medir cobertura de código
4. Personalizar reglas basadas en los estándares de tu equipo
5. Configurar notificaciones para fallos de puertas de calidad

## Opciones de Configuración Adicionales

### Reglas Personalizadas:
Agregar a sonar-project.properties:
```properties
# Deshabilitar reglas específicas
sonar.javascript.rule.S1234.disabled=true

# Establecer severidad de regla
sonar.javascript.rule.S5678.severity=INFO
```

### Múltiples Entornos:
Crear archivos de propiedades separados:
- sonar-project-dev.properties
- sonar-project-prod.properties

Usar con: `sonar-scanner -Dproject.settings=sonar-project-dev.properties`

## Recursos de Soporte

- [Documentación de SonarQube](https://docs.sonarqube.org/)
- [Documentación de SonarCloud](https://docs.sonarcloud.io/)
- [Análisis de JavaScript/TypeScript](https://docs.sonarqube.org/latest/analysis/languages/javascript/) 