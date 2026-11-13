:man_page: mongoc_srv_host_validator_t

mongoc_srv_host_validator_t
===========================

Synopsis
--------

.. code-block:: c

  typedef bool (*mongoc_srv_host_validator_t) (const char *host,
                                               void *user_data,
                                               bson_error_t *error);

The type of the callback set with :symbol:`mongoc_uri_set_srv_host_validator`. The driver calls it once for each host name returned by an SRV lookup to decide whether the host may be used.

.. warning::

   **Modifying the default SRV domain name validation can create vulnerabilities.** When a validator is set, its result is the only check applied to a returned host name. A validator that accepts hosts it should not allows an attacker who can control DNS responses to direct the driver to a host of their choosing.

Parameters
----------

* ``host``: The host name returned by the SRV lookup, after normalization (any trailing dot is removed, non-ASCII labels are converted to Punycode, and ASCII characters are lowercased). The string is only valid for the duration of the call.
* ``user_data``: The pointer given to :symbol:`mongoc_uri_set_srv_host_validator`.
* ``error``: An optional :symbol:`bson_error_t`. Set it before returning ``false`` to report that validation could not be completed, as opposed to the host being invalid.

Return
------

Returns ``true`` if the host is valid, and ``false`` otherwise. If the callback returns ``false`` after setting ``error``, the error message is included in the error reported by the driver.

Description
-----------

The validator replaces the driver's default validation of returned hosts entirely: the driver does not additionally check that the host shares the domain of the SRV hostname.

The callback is called while the client is created (initial DNS seedlist discovery) and again each time the driver polls the SRV record of a client in a :symbol:`mongoc_client_pool_t`.

* During initial discovery, a host that the validator rejects, or an error reported by the validator, causes creating the client or pool to fail.
* During SRV polling, a host that the validator rejects, or an error reported by the validator, causes the host to be ignored. No error is reported to the application, and the driver logs the failure.

The callback must not block, because it is called synchronously. In a client pool the callback may be called from a background thread, so it must be thread-safe.

.. seealso::

  | :symbol:`mongoc_uri_set_srv_host_validator`
  | :symbol:`mongoc_uri_t`
