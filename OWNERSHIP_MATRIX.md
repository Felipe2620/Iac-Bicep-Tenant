# Matriz de Ownership y Estado de Microsoft Entra ID / Azure IAM Lab

**Proyecto:** IAM Lab & Infrastructure as Code (Bicep)  
**Tenant ID:** `4c87468f-a072-4e8e-a8ad-f04e3884f1cd`  
**Tenant Name:** `Directorio predeterminado` (`LabConstSoftwareoutlook.onmicrosoft.com`)  
**Subscription ID:** `ddef8b16-d606-40e1-9479-bbe8cf74dbcb` (`Azure subscription 1`)  
**Fecha de Línea Base Inicial:** 2026-09-24  
**Última Actualización:** 2026-09-24  
**Estado:** Documento Vivo / Editable  

---

## 1. Propósito y Política de Edición

> [!NOTE]
> **Este documento es dinámico y editable.** A medida que los requerimientos del laboratorio evolucionen, los recursos cambien de estado o se descubran nuevas necesidades de integración (por ejemplo, adopción formal de cuentas existentes o delegación de permisos adicionales para el CSPM), esta matriz **debe ser actualizada** mediante commits en Git para mantener la trazabilidad de la gobernanza de identidades.

---

## 2. Clasificación de Categorías de Ownership

- **EXISTENTE / NO TOCAR:** Recursos preexistentes o identidades raíz cuyo ciclo de vida no depende de la infraestructura como código. IaC **no debe** modificarlos, reconfigurarlos ni destruirlos bajo ninguna circunstancia.
- **NUEVO / ADMINISTRADO POR IaC:** Recursos, identidades, grupos y asignaciones creados y gestionados 100% de manera declarativa mediante plantillas (Bicep / Microsoft Graph). Su ciclo de vida depende directamente del código.
- **EXISTENTE / FUTURAMENTE ADOPTABLE POR IaC:** Recursos u objetos creados previamente de forma manual pero que son candidatos a ser importados, referenciados o complementados con asignaciones declarativas en futuras iteraciones.

---

## 3. Matriz de Ownership

| Objeto / Recurso | Identificador / Object ID | Tipo | Clasificación | Reglas y Justificación |
| :--- | :--- | :--- | :--- | :--- |
| **Tenant & Dominio Base** | Tenant: `4c87468f-a072-4e8e-a8ad-f04e3884f1cd`<br>Dominio: `LabConstSoftwareoutlook.onmicrosoft.com` | Directorio Entra ID | **EXISTENTE / NO TOCAR** | Configuración raíz del tenant. Inmutable para el laboratorio. |
| **Lab ConstSoftware** | User ID: `be5ca056-e196-4b69-9889-226037119d2e`<br>UPN: `LabConstSoftware_outlook.com#EXT#@...` | Usuario (Invitado / Break-Glass) | **EXISTENTE / NO TOCAR** | Propietario original de la suscripción y Global Admin. Protegido ante cualquier acción de IaC. |
| **Rol `Owner` en Suscripción** | Role Assignment ID: `9fc42658-1654-4f4a-b658-e5f11ced7e8c` | Azure RBAC | **EXISTENTE / NO TOCAR** | Asignación directa sobre la suscripción `ddef8b16-d606-40e1-9479-bbe8cf74dbcb`. |
| **App Registration: `ID-Security-Validator`** | App ID: `81ebbc47-1df7-47b4-9368-21d7e8626dd7`<br>Object ID: `f03071e1-6ad9-4c4f-a11d-3cf430dafe1d` | App Registration | **EXISTENTE / NO TOCAR** | Identidad de la aplicación CSPM. Ciclo de vida y credenciales independientes. |
| **Service Principal: `ID-Security-Validator`** | SP Object ID: `f2513de7-ac0a-49b0-a669-87de9cbbdad3` | Enterprise Application | **EXISTENTE / NO TOCAR** | Instancia local del CSPM. Se preservan sus secretos (`Backend_Node_Secret`) y callbacks. |
| **Permisos Graph actuales del CSPM** | AppRole `Organization.Read.All` (`498476ce-...`)<br>Scope `User.Read` (`e1fe6dd8-...`) | Graph Permissions | **EXISTENTE / NO TOCAR** | Permisos mínimos de lectura otorgados previamente al CSPM. |
| **Usuario: `IaC_admin`** | User ID: `28a498ff-295b-4f9e-9241-a70128ad748f`<br>UPN: `IaC_admin@LabConstSoftwareoutlook.onmicrosoft.com` | Usuario Cloud Nativo | **EXISTENTE / FUTURAMENTE ADOPTABLE POR IaC** | Actualmente Global Admin manual. Puede usarse como cuenta de despliegue o ser formalizada vía código. |
| **Nuevas Asignaciones de Rol para el CSPM** | Pendiente (ej. Rol `Reader` en suscripción / scope Graph adicional) | Azure RBAC / Graph Role Assignment | **EXISTENTE / FUTURAMENTE ADOPTABLE POR IaC** | Si el CSPM requiere auditar recursos de Azure, IaC puede aprovisionar la *asignación* de rol sin alterar el Service Principal. |
| **Nuevos Usuarios del Lab** | Por definir en plantillas | Entra ID Users | **NUEVO / ADMINISTRADO POR IaC** | Cuentas simuladas para pruebas de privilegios mínimos y segregación de funciones. |
| **Grupos de Seguridad** | Por definir en plantillas | Entra ID Groups | **NUEVO / ADMINISTRADO POR IaC** | Grupos para asignación estructurada de accesos (ej. `Sec-DevOps`, `Sec-Auditors`). |
| **Resource Groups e Infraestructura** | Por desplegar en `ddef8b16-d606-40e1-9479-bbe8cf74dbcb` | Azure Resources | **NUEVO / ADMINISTRADO POR IaC** | Grupos de recursos de Azure y workloads de prueba creados mediante Bicep. |
| **Asignaciones de Roles de Recursos** | Por definir en plantillas | Azure RBAC | **NUEVO / ADMINISTRADO POR IaC** | Roles asignados sobre los recursos administrados por el laboratorio. |

---

## 4. Registro de Cambios (Changelog)

| Fecha | Versión | Autor / Responsable | Descripción del Cambio |
| :--- | :--- | :--- | :--- |
| 2026-09-24 | 1.0.0 | Antigravity & User | Creación de la línea base tras inspección no destructiva de Microsoft Entra ID. |
