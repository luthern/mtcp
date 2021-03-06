#   BSD LICENSE
#
#   Copyright 2015 6WIND S.A.
#   Copyright 2015 Mellanox.
#
#   Redistribution and use in source and binary forms, with or without
#   modification, are permitted provided that the following conditions
#   are met:
#
#     * Redistributions of source code must retain the above copyright
#       notice, this list of conditions and the following disclaimer.
#     * Redistributions in binary form must reproduce the above copyright
#       notice, this list of conditions and the following disclaimer in
#       the documentation and/or other materials provided with the
#       distribution.
#     * Neither the name of 6WIND S.A. nor the names of its
#       contributors may be used to endorse or promote products derived
#       from this software without specific prior written permission.
#
#   THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS
#   "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT
#   LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR
#   A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT
#   OWNER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL,
#   SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT
#   LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE,
#   DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY
#   THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
#   (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
#   OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

include $(RTE_SDK)/mk/rte.vars.mk

# Library name.
LIB = librte_pmd_mlx5.a

# Sources.
SRCS-$(CONFIG_RTE_LIBRTE_MLX5_PMD) += mlx5.c
SRCS-$(CONFIG_RTE_LIBRTE_MLX5_PMD) += mlx5_rxq.c
SRCS-$(CONFIG_RTE_LIBRTE_MLX5_PMD) += mlx5_txq.c
SRCS-$(CONFIG_RTE_LIBRTE_MLX5_PMD) += mlx5_rxtx.c
SRCS-$(CONFIG_RTE_LIBRTE_MLX5_PMD) += mlx5_trigger.c
SRCS-$(CONFIG_RTE_LIBRTE_MLX5_PMD) += mlx5_ethdev.c
SRCS-$(CONFIG_RTE_LIBRTE_MLX5_PMD) += mlx5_mac.c
SRCS-$(CONFIG_RTE_LIBRTE_MLX5_PMD) += mlx5_rxmode.c
SRCS-$(CONFIG_RTE_LIBRTE_MLX5_PMD) += mlx5_vlan.c
SRCS-$(CONFIG_RTE_LIBRTE_MLX5_PMD) += mlx5_stats.c
SRCS-$(CONFIG_RTE_LIBRTE_MLX5_PMD) += mlx5_rss.c
SRCS-$(CONFIG_RTE_LIBRTE_MLX5_PMD) += mlx5_fdir.c

# Dependencies.
DEPDIRS-$(CONFIG_RTE_LIBRTE_MLX5_PMD) += lib/librte_ether
DEPDIRS-$(CONFIG_RTE_LIBRTE_MLX5_PMD) += lib/librte_mbuf
DEPDIRS-$(CONFIG_RTE_LIBRTE_MLX5_PMD) += lib/librte_eal
DEPDIRS-$(CONFIG_RTE_LIBRTE_MLX5_PMD) += lib/librte_mempool

# Basic CFLAGS.
CFLAGS += -O3
CFLAGS += -std=gnu99 -Wall -Wextra
CFLAGS += -g
CFLAGS += -I.
CFLAGS += -D_XOPEN_SOURCE=600
CFLAGS += $(WERROR_FLAGS)
CFLAGS += -Wno-strict-prototypes
LDLIBS += -libverbs

# A few warnings cannot be avoided in external headers.
CFLAGS += -Wno-error=cast-qual

EXPORT_MAP := rte_pmd_mlx5_version.map
LIBABIVER := 1

# DEBUG which is usually provided on the command-line may enable
# CONFIG_RTE_LIBRTE_MLX5_DEBUG.
ifeq ($(DEBUG),1)
CONFIG_RTE_LIBRTE_MLX5_DEBUG := y
endif

# User-defined CFLAGS.
ifeq ($(CONFIG_RTE_LIBRTE_MLX5_DEBUG),y)
CFLAGS += -pedantic -UNDEBUG -DPEDANTIC
else
CFLAGS += -DNDEBUG -UPEDANTIC
endif

ifdef CONFIG_RTE_LIBRTE_MLX5_SGE_WR_N
CFLAGS += -DMLX5_PMD_SGE_WR_N=$(CONFIG_RTE_LIBRTE_MLX5_SGE_WR_N)
endif

ifdef CONFIG_RTE_LIBRTE_MLX5_MAX_INLINE
CFLAGS += -DMLX5_PMD_MAX_INLINE=$(CONFIG_RTE_LIBRTE_MLX5_MAX_INLINE)
endif

ifdef CONFIG_RTE_LIBRTE_MLX5_TX_MP_CACHE
CFLAGS += -DMLX5_PMD_TX_MP_CACHE=$(CONFIG_RTE_LIBRTE_MLX5_TX_MP_CACHE)
endif

include $(RTE_SDK)/mk/rte.lib.mk

# Generate and clean-up mlx5_autoconf.h.

export CC CFLAGS CPPFLAGS EXTRA_CFLAGS EXTRA_CPPFLAGS
export AUTO_CONFIG_CFLAGS = -Wno-error

ifndef V
AUTOCONF_OUTPUT := >/dev/null
endif

mlx5_autoconf.h: $(RTE_SDK)/scripts/auto-config-h.sh
	$Q $(RM) -f -- '$@'
	$Q sh -- '$<' '$@' \
		HAVE_EXP_QUERY_DEVICE \
		infiniband/verbs.h \
		type 'struct ibv_exp_device_attr' $(AUTOCONF_OUTPUT)
	$Q sh -- '$<' '$@' \
		HAVE_FLOW_SPEC_IPV6 \
		infiniband/verbs.h \
		type 'struct ibv_exp_flow_spec_ipv6' $(AUTOCONF_OUTPUT)
	$Q sh -- '$<' '$@' \
		HAVE_EXP_QP_BURST_CREATE_ENABLE_MULTI_PACKET_SEND_WR \
		infiniband/verbs.h \
		enum IBV_EXP_QP_BURST_CREATE_ENABLE_MULTI_PACKET_SEND_WR \
		$(AUTOCONF_OUTPUT)
	$Q sh -- '$<' '$@' \
		HAVE_EXP_DEVICE_ATTR_VLAN_OFFLOADS \
		infiniband/verbs.h \
		enum IBV_EXP_DEVICE_ATTR_VLAN_OFFLOADS \
		$(AUTOCONF_OUTPUT)
	$Q sh -- '$<' '$@' \
		HAVE_EXP_CQ_RX_TCP_PACKET \
		infiniband/verbs.h \
		enum IBV_EXP_CQ_RX_TCP_PACKET \
		$(AUTOCONF_OUTPUT)
	$Q sh -- '$<' '$@' \
		HAVE_VERBS_FCS \
		infiniband/verbs.h \
		enum IBV_EXP_CREATE_WQ_FLAG_SCATTER_FCS \
		$(AUTOCONF_OUTPUT)
	$Q sh -- '$<' '$@' \
		HAVE_VERBS_RX_END_PADDING \
		infiniband/verbs.h \
		enum IBV_EXP_CREATE_WQ_FLAG_RX_END_PADDING \
		$(AUTOCONF_OUTPUT)
	$Q sh -- '$<' '$@' \
		HAVE_VERBS_VLAN_INSERTION \
		infiniband/verbs.h \
		enum IBV_EXP_RECEIVE_WQ_CVLAN_INSERTION \
		$(AUTOCONF_OUTPUT)

$(SRCS-$(CONFIG_RTE_LIBRTE_MLX5_PMD):.c=.o): mlx5_autoconf.h

clean_mlx5: FORCE
	$Q rm -f -- mlx5_autoconf.h

clean: clean_mlx5
