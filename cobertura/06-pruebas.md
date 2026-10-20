---
titulo: Cobertura de pruebas
tipo: matriz
area: cobertura
version: 1.0.0
estado: vigente
autor: php artisan docs:mapa
fecha: 2026-10-20
commit_auditado: no-aplica
alcance:
  - tests/
requisitos: []
---

# Cobertura de pruebas

Inventario de las pruebas automatizadas y el documento que las cita.

> Documento **generado** por `php artisan docs:mapa`. No editar a mano: cualquier cambio se pierde.
> Para corregir una fila, corrige el código, el `alcance` del documento correspondiente o el propio comando.

- Se genera con `php artisan docs:mapa`; no editar a mano.
- Archivos de prueba: **15**. Sin documento: **15**.
- Métodos de prueba: **56**.

---
| Archivo | Clase | Métodos de prueba | Cantidad | Documento | Estado |
|---|---|---|---|---|---|
| `tests\Feature\AdminTest.php` | `Tests\Feature\AdminTest` | `test_admin_area_requires_authentication_and_role`, `test_cliente_cannot_access_admin_area`, `test_admin_creates_a_category`, `test_admin_creates_a_product_with_image` | 4 | — | sin-documentar |
| `tests\Feature\AuthTest.php` | `Tests\Feature\AuthTest` | `test_login_screen_can_be_rendered`, `test_users_can_authenticate`, `test_users_cannot_authenticate_with_invalid_password`, `test_new_users_can_register` | 4 | — | sin-documentar |
| `tests\Feature\CartTest.php` | `Tests\Feature\CartTest` | `test_cart_page_requires_authentication`, `test_add_to_cart_stores_the_product`, `test_confirmar_creates_order_with_frozen_prices_and_comprobante`, `test_preferential_discount_is_applied`, `test_product_without_price_cannot_be_added_to_cart`, `test_confirming_order_with_unpriced_product_is_rejected` | 6 | — | sin-documentar |
| `tests\Feature\CatalogTest.php` | `Tests\Feature\CatalogTest` | `test_home_lists_only_featured_products`, `test_catalog_lists_only_active_products`, `test_catalog_search_filters_results`, `test_catalog_filters_by_category`, `test_catalog_page_renders_embedded_component`, `test_product_detail_loads_by_slug`, `test_downloads_page_links_both_documents`, `test_product_without_price_shows_por_consultar` | 8 | — | sin-documentar |
| `tests\Feature\ContactoTest.php` | `Tests\Feature\ContactoTest` | `test_contact_form_stores_message_and_sends_mail`, `test_contact_form_requires_consent`, `test_contact_page_renders` | 3 | — | sin-documentar |
| `tests\Feature\ExampleTest.php` | `Tests\Feature\ExampleTest` | `test_the_application_returns_a_successful_response` | 1 | — | sin-documentar |
| `tests\Feature\FaseSieteTest.php` | `Tests\Feature\FaseSieteTest` | `test_stock_request_is_stored`, `test_reclamacion_is_registered_with_correlative_and_copies`, `test_legal_pages_render`, `test_admin_pages_render_for_administrador` | 4 | — | sin-documentar |
| `tests\Feature\PedidoAdminTest.php` | `Tests\Feature\PedidoAdminTest` | `test_admin_can_advance_order_state_with_audit`, `test_invalid_state_transition_is_rejected` | 2 | — | sin-documentar |
| `tests\Feature\PedidoTest.php` | `Tests\Feature\PedidoTest` | `test_client_can_view_and_download_their_comprobante`, `test_client_cannot_view_another_clients_order` | 2 | — | sin-documentar |
| `tests\Feature\PerfilTest.php` | `Tests\Feature\PerfilTest` | `test_perfil_page_requires_authentication`, `test_perfil_page_renders_for_authenticated_user`, `test_user_can_update_profile_and_contact_data`, `test_user_can_change_password_with_correct_current_password`, `test_password_change_fails_with_wrong_current_password`, `test_document_number_must_be_unique_across_clients` | 6 | — | sin-documentar |
| `tests\Feature\RbacTest.php` | `Tests\Feature\RbacTest` | `test_administrador_puede_gestionar_catalogo`, `test_operador_no_gestiona_catalogo_pero_accede_al_panel`, `test_cliente_no_accede_al_panel_administrativo`, `test_cliente_solo_ve_sus_propios_pedidos` | 4 | — | sin-documentar |
| `tests\Feature\SeguridadTest.php` | `Tests\Feature\SeguridadTest` | `test_security_headers_are_present`, `test_cliente_cannot_access_admin_product_management`, `test_guest_cannot_access_private_area`, `test_invalid_file_upload_is_rejected` | 4 | — | sin-documentar |
| `tests\Feature\ValidarDocumentacionTest.php` | `Tests\Feature\ValidarDocumentacionTest` | `test_la_documentacion_del_repositorio_cumple_el_estandar`, `test_falla_cuando_el_front_matter_esta_incompleto`, `test_falla_cuando_hay_un_encabezado_duplicado`, `test_falla_cuando_el_mapa_de_cobertura_esta_desactualizado`, `test_falla_cuando_no_existe_el_mapa_de_cobertura`, `test_el_mapa_inventaria_clases_metodos_y_vistas`, `test_falla_cuando_un_enlace_relativo_no_existe` | 7 | — | sin-documentar |
| `tests\TestCase.php` | `Tests\TestCase` | `` | 0 | — | sin-documentar |
| `tests\Unit\ExampleTest.php` | `Tests\Unit\ExampleTest` | `test_that_true_is_true` | 1 | — | sin-documentar |
