<template>
    <table class="border-collapse table-fixed w-full text-sm">
        <thead class="bg-gray-300 text-left text-xs font-medium uppercase tracking-wider">
            <slot name="theader"></slot>
        </thead>
        <tbody class="bg-white rounded-[12rem] border-2 border-[#000]">
            <slot name="tbody"></slot>
        </tbody>
    </table>
</template>
