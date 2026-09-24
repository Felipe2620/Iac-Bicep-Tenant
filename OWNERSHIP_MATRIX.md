# Matriz de Ownership y Estado de Microsoft Entra ID / Azure IAM Lab

**Proyecto:** IAM Lab & Infrastructure as Code (Bicep)  
**Tenant ID:** `4c87468f-a072-4e8e-a8ad-f04e3884f1cd`  
**Tenant Name:** `Directorio predeterminado` (`LabConstSoftwareoutlook.onmicrosoft.com`)  
**Subscription ID:** `ddef8b16-d606-40e1-9479-bbe8cf74dbcb` (`Azure subscription 1`)  
**Fecha de Línea Base Inicial:** 2026-09-24  
**Última Actualización:** 2026-09-24 (v1.1.0)  
**Estado:** Documento Vivo / Sujeto a Control de Cambios en Git  

---

## 1. Propósito y Política de Gobierno

> [!NOTE]
> **Este documento es dinámico y auditable.** Todo cambio en el alcance, reclasificación de componentes o ciclo de vida de identidades e infraestructura debe quedar registrado mediante commits en el repositorio.
> 
> **Regla de Oro de Seguridad:** Ningún archivo versionado (`.md`, `.bicep`, `.bicepparam`, etc.) debe almacenar valores confidenciales, contraseñas o secretos planos. Toda mención a credenciales en este documento corresponde exclusivamente a nombres de metadatos lógicos consultados desde el plano de control de Azure/Microsoft Graph.

---

## 2. Clasificación de Categorías de Ownership

- **EXISTENTE / NO TOCAR:** Recursos preexistentes, cuentas administrativas e identidades del sistema cuyo ciclo de vida **no pertenece** al laboratorio de IaC. Las plantillas de IaC **nunca** deben alterarlos, modificarlos, reasignarles propiedades ni destruirlos.
- **NUEVO / ADMINISTRADO POR IaC:** Recursos de Azure (ARM), grupos, identidades y asignaciones declarados y creados 100% por código en este repositorio. Su ciclo de vida completo (creación, actualización y destrucción) es gobernado por Bicep.
- **EXISTENTE / FUTURAMENTE ADOPTABLE POR IaC:** Recursos creados manualmente en etapas tempranas que podrían ser importados o referenciados formalmente en el futuro tras un análisis de impacto. *(Actualmente vacía bajo política de estricta separación de privilegios).*

---

## 3. Matriz de Ownership Validada

| Objeto / Recurso | Identificador / Object ID | Tipo | Clasificación | Reglas de Protección y Justificación |
| :--- | :--- | :--- | :--- | :--- |
| **Tenant & Dominio Base** | Tenant: `4c87468f-a072-4e8e-a8ad-f04e3884f1cd`<br>Dominio: `LabConstSoftwareoutlook.onmicrosoft.com` | Directorio Entra ID | **EXISTENTE / NO TOCAR** | Configuración raíz del tenant. Inmutable para el laboratorio. |
| **Lab ConstSoftware** | User ID: `be5ca056-e196-4b69-9889-226037119d2e`<br>UPN: `LabConstSoftware_outlook.com#EXT#@...` | Usuario (Invitado / Break-Glass) | **EXISTENTE / NO TOCAR** | Cuenta propietaria original de la suscripción y Global Administrator. Identidad de emergencia humana. Protegida ante cualquier acción de IaC. |
| **Usuario: `IaC_admin`** | User ID: `28a498ff-295b-4f9e-9241-a70128ad748f`<br>UPN: `IaC_admin@LabConstSoftwareoutlook.onmicrosoft.com` | Usuario Cloud Nativo | **EXISTENTE / NO TOCAR** | Cuenta administrativa existente con rol Global Administrator. **No se adopta por IaC** para evitar riesgos de sobreescritura accidental o acoplamiento indebido. A futuro se estudiará una identidad de deployment dedicada con mínimo privilegio. |
| **Rol `Owner` en Suscripción** | Role Assignment ID: `9fc42658-1654-4f4a-b658-e5f11ced7e8c` | Azure RBAC | **EXISTENTE / NO TOCAR** | Asignación directa sobre la suscripción `ddef8b16-d606-40e1-9479-bbe8cf74dbcb` para la cuenta de emergencia/propietaria. |
| **CSPM: App Registration (`ID-Security-Validator`)** | App ID: `81ebbc47-1df7-47b4-9368-21d7e8626dd7`<br>Object ID: `f03071e1-6ad9-4c4f-a11d-3cf430dafe1d` | App Registration | **EXISTENTE / NO TOCAR** | Identidad de la aplicación auditora/CSPM. Completamente fuera del ownership del IAM Lab. |
| **CSPM: Service Principal (`ID-Security-Validator`)** | SP Object ID: `f2513de7-ac0a-49b0-a669-87de9cbbdad3` | Enterprise Application | **EXISTENTE / NO TOCAR** | Instancia empresarial del CSPM. Se preservan intactos sus callbacks (`http://localhost:3000/login/callback`) y su referencia de credencial lógica (`Backend_Node_Secret` — *metadato lógico del portal, no valor secreto*). |
| **CSPM: Permisos Graph Actuales** | AppRole `Organization.Read.All` (`498476ce-...`)<br>Scope `User.Read` (`e1fe6dd8-...`) | Permisos MS Graph | **EXISTENTE / NO TOCAR** | Permisos otorgados externamente al CSPM. IaC no modificará, revocará ni extenderá permisos sobre este SP sin requerimiento explícito independiente. |
| **Nuevos Grupos de Seguridad** | A definir en plantillas Bicep | Entra ID Groups | **NUEVO / ADMINISTRADO POR IaC** | Grupos para RBAC y control de acceso (ej. `sec-iam-readers`, `sec-iam-operators`). |
| **Nuevos Usuarios / Cuentas de Prueba** | A definir en plantillas Bicep | Entra ID Users | **NUEVO / ADMINISTRADO POR IaC** | Identidades sintéticas para validación de privilegios mínimos y pruebas de auditoría. |
| **Resource Groups e Infraestructura** | Por desplegar en `ddef8b16-d606-40e1-9479-bbe8cf74dbcb` | Azure Resources (ARM) | **NUEVO / ADMINISTRADO POR IaC** | Grupos de recursos y servicios Azure de soporte gobernados por Bicep. |
| **Asignaciones de Roles de Recursos (RBAC)** | Por definir en plantillas Bicep | Azure RBAC | **NUEVO / ADMINISTRADO POR IaC** | Asignaciones de roles estrictamente sobre los recursos y grupos gestionados por el laboratorio. |

---

## 4. Registro de Cambios (Changelog)

| Fecha | Versión | Autor / Responsable | Descripción del Cambio |
| :--- | :--- | :--- | :--- |
| 2026-09-24 | 1.0.0 | Antigravity & User | Creación de la línea base tras inspección no destructiva de Microsoft Entra ID. |
| 2026-09-24 | 1.1.0 | Antigravity & User | **Reclasificación de seguridad:**<br>1. `IaC_admin` reclasificado a `EXISTENTE / NO TOCAR`.<br>2. CSPM totalmente excluido del laboratorio en todos sus componentes (`EXISTENTE / NO TOCAR`).<br>3. Aclaración explícita de metadato lógico para `Backend_Node_Secret`. |
