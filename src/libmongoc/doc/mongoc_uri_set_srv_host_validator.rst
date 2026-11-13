:man_page: mongoc_uri_set_srv_host_validator

mongoc_uri_set_srv_host_validator()
===================================

Synopsis
--------

.. code-block:: c

  bool
  mongoc_uri_set_srv_host_validator (mongoc_uri_t *uri,
                                     mongoc_srv_host_validator_t validator,
                                     void *user_data,
                                     bson_error_t *error);

Parameters
----------

* ``uri``: A :symbol:`mongoc_uri_t`.
* ``validator``: A :symbol:`mongoc_srv_host_validator_t`, or ``NULL`` to remove a previously set validator.
* ``user_data``: A pointer passed to ``validator`` on every call. It is not owned by the URI.
* ``error``: An optional :symbol:`bson_error_t`.

Description
-----------

Sets a callback that validates each host name returned by the SRV lookup of a "mongodb+srv" URI. The validator replaces the driver's default validation, and is an alternative to the ``srvAllowedHostsSuffix`` URI option.

.. warning::

   **Modifying the default SRV domain name validation can create vulnerabilities.** Once a validator is set, the driver relies entirely on it to reject hosts returned by DNS.

The validator is not a connection string option, and can only be set with this function. It is used by every :symbol:`mongoc_client_t` and :symbol:`mongoc_client_pool_t` created from the URI after this function is called, and is retained when the URI is copied. Changing the validator of a :symbol:`mongoc_uri_t` does not affect clients and pools that were already created from it.

``user_data`` must remain valid until every client and pool created from the URI is destroyed, even if the URI itself is destroyed earlier.

The validator may be called from a background thread. See :symbol:`mongoc_srv_host_validator_t`.

Returns
-------

Returns ``true`` if the validator was set. Returns ``false`` and sets ``error`` if:

* ``uri`` is not a "mongodb+srv" URI.
* The URI has the ``srvAllowedHostsSuffix`` option. A validator and ``srvAllowedHostsSuffix`` cannot be used together.

When this function returns ``false``, the URI is not modified.

.. seealso::

  | :symbol:`mongoc_srv_host_validator_t`
  | :symbol:`mongoc_uri_t`
