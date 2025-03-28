#
# Copyright (c) 2024 Nordic Semiconductor
#
# SPDX-License-Identifier: LicenseRef-Nordic-5-Clause
#

zephyr_interface_library_named(hostap_crypto)

set(HOSTAP_BASE ${ZEPHYR_HOSTAP_MODULE_DIR})
set(WIFI_NM_WPA_SUPPLICANT_BASE ${HOSTAP_BASE}/wpa_supplicant)
set(HOSTAP_SRC_BASE ${HOSTAP_BASE}/src)
set(WIFI_NM_HOSTAPD_BASE ${HOSTAP_BASE}/hostapd)

set(CMAKE_EXE_LINKER_FLAGS "--specs=nosys.specs -lnosys")
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -DMISSING_SYSCALL_NAMES")

# For src/utils includes
target_link_libraries(hostap_crypto INTERFACE hostap)

if(DEFINED CONFIG_HOSTAP_CRYPTO_LEGACY OR
   DEFINED CONFIG_HOSTAP_CRYPTO_LEGACY_PSA)
  zephyr_library_sources(
    ${HOSTAP_SRC_BASE}/crypto/crypto_mbedtls-bignum.c
    ${HOSTAP_SRC_BASE}/crypto/crypto_mbedtls-ec.c
    ${HOSTAP_SRC_BASE}/crypto/crypto_mbedtls.c
    ${HOSTAP_SRC_BASE}/crypto/aes-internal.c
    ${HOSTAP_SRC_BASE}/crypto/aes-wrap.c
    ${HOSTAP_SRC_BASE}/crypto/aes-unwrap.c
    ${HOSTAP_SRC_BASE}/crypto/rc4.c
    ${HOSTAP_SRC_BASE}/crypto/sha1-internal.c
    ${HOSTAP_SRC_BASE}/crypto/sha1-prf.c
    ${HOSTAP_SRC_BASE}/crypto/sha1-tlsprf.c
    ${HOSTAP_SRC_BASE}/crypto/sha256-prf.c
    ${HOSTAP_SRC_BASE}/crypto/sha256-kdf.c
    ${HOSTAP_SRC_BASE}/crypto/sha384-prf.c
    ${HOSTAP_SRC_BASE}/crypto/sha384-kdf.c
    ${HOSTAP_SRC_BASE}/crypto/sha512-internal.c
    ${HOSTAP_SRC_BASE}/crypto/sha512.c
    ${HOSTAP_SRC_BASE}/crypto/sha512-prf.c
    ${HOSTAP_SRC_BASE}/crypto/sha512-kdf.c
  )

  zephyr_library_sources_ifdef(CONFIG_HOSTAP_WPA3
    ${HOSTAP_SRC_BASE}/crypto/sha256-kdf.c
  )

  zephyr_library_sources_ifndef(CONFIG_HOSTAP_CRYPTO_ENTERPRISE
    ${HOSTAP_SRC_BASE}/crypto/tls_none.c
  )

  zephyr_library_sources_ifdef(CONFIG_HOSTAP_CRYPTO_ENTERPRISE
    # common
    ${HOSTAP_SRC_BASE}/crypto/sha384-tlsprf.c
    ${HOSTAP_SRC_BASE}/crypto/sha256-tlsprf.c
    ${HOSTAP_SRC_BASE}/crypto/sha1-tlsprf.c
    ${HOSTAP_SRC_BASE}/crypto/sha1-tprf.c
    ${HOSTAP_SRC_BASE}/crypto/ms_funcs.c
    ${HOSTAP_SRC_BASE}/crypto/aes-eax.c
    # MD4 removed from MbedTLS
    ${HOSTAP_SRC_BASE}/crypto/md4-internal.c
    ${HOSTAP_SRC_BASE}/crypto/aes-encblock.c
    ${HOSTAP_SRC_BASE}/crypto/tls_mbedtls.c
  )
endif()

if(DEFINED CONFIG_HOSTAP_CRYPTO_ALT_LEGACY)
  zephyr_include_directories(
    ${HOSTAP_BASE}/port/mbedtls
  )

  zephyr_library_sources(
    ${HOSTAP_SRC_BASE}/crypto/crypto_mbedtls_alt.c
    ${HOSTAP_SRC_BASE}/crypto/rc4.c
    ${HOSTAP_SRC_BASE}/crypto/aes-wrap.c
    ${HOSTAP_SRC_BASE}/crypto/aes-unwrap.c
    ${HOSTAP_SRC_BASE}/crypto/aes-internal-dec.c
    ${HOSTAP_SRC_BASE}/crypto/aes-internal.c
    ${HOSTAP_SRC_BASE}/crypto/aes-internal-enc.c
  )

  zephyr_library_sources_ifdef(CONFIG_HOSTAP_CRYPTO_MBEDTLS_PSA
    ${HOSTAP_BASE}/port/mbedtls/supp_psa_api.c
  )

  zephyr_library_sources_ifdef(CONFIG_HOSTAP_CRYPTO_ENTERPRISE
    ${HOSTAP_SRC_BASE}/crypto/ms_funcs.c
    ${HOSTAP_SRC_BASE}/crypto/aes-eax.c
    ${HOSTAP_SRC_BASE}/crypto/md4-internal.c
    ${HOSTAP_SRC_BASE}/crypto/sha1-internal.c
    ${HOSTAP_SRC_BASE}/crypto/fips_prf_internal.c
    ${HOSTAP_SRC_BASE}/crypto/milenage.c
    ${HOSTAP_SRC_BASE}/crypto/tls_mbedtls_alt.c
  )

  zephyr_library_sources_ifndef(CONFIG_HOSTAP_CRYPTO_ENTERPRISE
    ${HOSTAP_SRC_BASE}/crypto/tls_none.c
  )


  zephyr_library_sources_ifdef(CONFIG_HOSTAP_CRYPTO_TEST
    ${HOSTAP_SRC_BASE}/crypto/crypto_module_tests.c
    ${HOSTAP_SRC_BASE}/crypto/fips_prf_internal.c
    ${HOSTAP_SRC_BASE}/crypto/sha1-internal.c
    ${HOSTAP_SRC_BASE}/crypto/sha1-tlsprf.c
  )
endif()

if(DEFINED CONFIG_HOSTAP_CRYPTO_ALT_PSA)
  # Source code still uses the original symbol
  zephyr_compile_definitions(
    CONFIG_WIFI_NM_WPA_SUPPLICANT_CRYPTO_MBEDTLS_PSA
  )

  zephyr_include_directories(
    ${HOSTAP_BASE}/port/mbedtls
  )

  zephyr_library_sources(
    ${HOSTAP_SRC_BASE}/crypto/aes-wrap.c
    ${HOSTAP_SRC_BASE}/crypto/aes-unwrap.c
    ${HOSTAP_SRC_BASE}/crypto/aes-internal-dec.c
    ${HOSTAP_SRC_BASE}/crypto/aes-internal.c
    ${HOSTAP_SRC_BASE}/crypto/aes-internal-enc.c
    ${HOSTAP_SRC_BASE}/crypto/rc4.c
    ${HOSTAP_SRC_BASE}/crypto/crypto_mbedtls_alt.c
    ${HOSTAP_SRC_BASE}/crypto/sha256-kdf.c
    ${HOSTAP_BASE}/port/mbedtls/supp_psa_api.c
    ${HOSTAP_SRC_BASE}/crypto/tls_none.c
  )
endif()
