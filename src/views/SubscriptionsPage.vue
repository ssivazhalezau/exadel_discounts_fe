<template>
  <v-content>
    <SubscriptionsMobile class="hidden-md-and-up"/>
    <Subscriptions class="hidden-sm-and-down ml-10 mr-10"/>
  </v-content>
</template>

<script>
import Subscriptions from "@/components/Subscriptions/Subscriptions";
import SubscriptionsMobile from "@/components/Subscriptions/SubscriptionsMobile";

export default {
  name: "SubscriptionsPage",
  components: {SubscriptionsMobile, Subscriptions},
  watch: {
    $route: {
      immediate: true,
      handler(to, from) {
        document.title = to.meta.title || 'Subscriptions';
      }
    },
  }
};
</script>
