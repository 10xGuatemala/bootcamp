<!-- Destino sugerido: docs/sprints/YYYY-SXX-goal.md -->

# Sprint Goal — {{sprint}}

## Enunciado

> **{{Una frase que describa el resultado de negocio al final del sprint}}**
>
> Ejemplo: *"Al final del sprint, los usuarios admin pueden activar MFA desde su perfil y el sistema obliga a usarlo en el siguiente login."*

## Por qué ahora

{{Justificación corta: incidente, regulación, oportunidad comercial, dependencia externa.}}

## Cómo sabemos que lo logramos

Criterios **verificables** y **medibles** al cierre del sprint:

- [ ] El 100% de usuarios admin tienen MFA vinculado (medición: query a tabla `user_mfa_factors`).
- [ ] Ningún login admin tiene éxito sin segundo factor (medición: logs de auth, últimas 24h).
- [ ] El tiempo medio para activar TOTP es < 2 minutos (medición: tracking del flujo nuevo).

## Qué NO está en el alcance

Declarar explícitamente para evitar scope creep:

- MFA para roles no admin.
- Factores distintos de TOTP (SMS, WebAuthn).
- Política de expiración de backup codes.

## Dependencias externas

- Equipo de infraestructura: provisionar el secreto compartido para la app móvil antes del día 3.
- Legal: aprobar texto del email de "nuevo dispositivo" antes del día 5.
