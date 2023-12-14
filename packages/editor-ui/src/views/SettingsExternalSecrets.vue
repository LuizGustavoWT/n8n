<script lang="ts" setup>
import { useUIStore } from '@/stores/ui.store';
import { useI18n } from '@/composables/useI18n';
import { useToast } from '@/composables/useToast';
import { useExternalSecretsStore } from '@/stores/externalSecrets.ee.store';
import { computed, onMounted } from 'vue';
import ExternalSecretsProviderCard from '@/components/ExternalSecretsProviderCard.ee.vue';
import type { ExternalSecretsProvider } from '@/Interface';

const i18n = useI18n();
const uiStore = useUIStore();
const externalSecretsStore = useExternalSecretsStore();
const toast = useToast();

const sortedProviders = computed(() => {
	return ([...externalSecretsStore.providers] as ExternalSecretsProvider[]).sort((a, b) => {
		return b.name.localeCompare(a.name);
	});
});

onMounted(() => {
	try {
		void externalSecretsStore.fetchAllSecrets();
		void externalSecretsStore.getProviders();
	} catch (error) {
		toast.showError(error, i18n.baseText('error'));
	}
});

function goToUpgrade() {
	void uiStore.goToUpgrade('external-secrets', 'upgrade-external-secrets');
}
</script>

<template>
	<div class="pb-3xl">
		<n8n-heading size="2xlarge">{{ i18n.baseText('settings.externalSecrets.title') }}</n8n-heading>
		<div
			data-test-id="external-secrets-content-licensed"
		>
			<n8n-callout theme="secondary" class="mt-2xl mb-l">
				{{ i18n.baseText('settings.externalSecrets.info') }}
				<a :href="i18n.baseText('settings.externalSecrets.docs')" target="_blank">
					{{ i18n.baseText('settings.externalSecrets.info.link') }}
				</a>
			</n8n-callout>
			<ExternalSecretsProviderCard
				v-for="provider in sortedProviders"
				:key="provider.name"
				:provider="provider"
			/>
		</div>
	</div>
</template>
