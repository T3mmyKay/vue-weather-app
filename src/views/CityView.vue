<template>
    <div>
        <Suspense>
            <AsyncCityView/>
            <template #fallback>
                <CityVIewSkeleton/>
            </template>
        </Suspense>
    </div>
</template>

<script setup>

import AsyncCityView from "@/components/AsyncCityView.vue";
import CityVIewSkeleton from "@/components/CityVIewSkeleton.vue";
</script>
