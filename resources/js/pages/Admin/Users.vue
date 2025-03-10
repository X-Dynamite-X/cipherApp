<script setup lang="ts">
import DynamicEdit from '@/components/Model/DynamicEdit.vue';
import DataTable from '@/components/Table/DataTable.vue';
import TBody from '@/components/Table/TBody.vue';
import Thedar from '@/components/Table/Thedar.vue';
import { Button } from '@/components/ui/button';
import AppLayout from '@/layouts/AppLayout.vue';
import { type BreadcrumbItem } from '@/types';
import { Head, usePage } from '@inertiajs/vue3';
import { PencilIcon, TrashIcon } from 'lucide-vue-next';
import { computed, ref } from 'vue';

// تعريف الأنواع
interface ColumnName {
    name: string;
    id?: string | number;
}

interface Column {
    key: string;
    label: string;
    name?: string;
    type?: string;
    showInCreate?: boolean;
    showInEdit?: boolean;
    required?: boolean;
    disabled?: boolean;
    placeholder?: string;
    options?: Array<{ label: string; value: string }>;
    showInTabel?: boolean;
}

interface ModelData {
    id?: number;
    name?: string;
    email?: string;
    roles?: Array<{ name: string }>;
    [key: string]: any;
}

interface User {
    id: number;
    name: string;
    email: string;
    roles?: Array<{ name: string }>;
}

const breadcrumbs: BreadcrumbItem[] = [
    {
        title: 'Users',
        href: '/users',
    },
];

const columnsName: ColumnName[] = [{ name: '#' }, { name: 'Name' }, { name: 'Email' }, { name: 'Actions' }];

const columns: Column[] = [
    { key: 'id', label: 'ID', showInTabel: true },
    {
        key: 'actions',
        label: 'Actions',
    },
    {
        key: 'name',
        label: 'Name',
        name: 'name',
        type: 'text',
        required: true,
        disabled: false,
        showInCreate: true,
        showInTabel: true,
        showInEdit: true,
        placeholder: 'Enter Name',
    },
    {
        key: 'email',
        label: 'Email',
        name: 'email',
        type: 'email',
        showInCreate: true,
        showInTabel: true,
        showInEdit: true,
        required: true,
        disabled: false,
        placeholder: 'Enter Email',
    },
    {
        key: 'password',
        label: 'Password',
        name: 'password',
        type: 'password',
        showInCreate: true,
        showInEdit: false,
        required: true,
        showInTabel: false,
        disabled: true,
        placeholder: 'Enter Password',
    },
    {
        key: 'password_confirmation',
        label: 'Password Confirmation',
        name: 'password_confirmation',
        showInTabel: false,
        type: 'password',
        showInCreate: true,
        showInEdit: false,
        required: true,
        disabled: true,
        placeholder: 'Enter password Confirmation',
    },
];

const page = usePage<{ users: User[] }>();
const users = computed(() => page.props.users);

const showEditModel = ref(false);
const modelData = ref<ModelData>({});

const openEditModel = (data: ModelData) => {
    showEditModel.value = true;
    modelData.value = { ...data };
};

const updateData = (updatedData: ModelData) => {
    console.log(updatedData);
    closeModal();
    modelData.value = { ...updatedData };
};

const closeModal = () => {
    showEditModel.value = false;
};
</script>

<template>
  <Head title="Users" />

  <AppLayout :breadcrumbs="breadcrumbs">
    <div class="flex h-full flex-1 flex-col gap-4 p-4">
      <div
        class="relative overflow-hidden rounded-xl border bg-card text-card-foreground shadow"
      >
        <div class="overflow-x-auto">
          <DataTable
            :data="users"
            :columnsName="columnsName"
            :columns="columns"
            :loading="false"
            id="users-table"
            :availableData="true"
            @sort="() => {}"
          >
            <template #header>
              <Thedar
                v-for="column in columnsName"
                :nameFeild="column.name"
                :key="column.id"
              />
            </template>
            <template #row="{ item }">
              <TBody :item="item" :columns="columns">
                <template #column-actions="{ item }">
                  <Button
                    variant="outline"
                    size="sm"
                    :id="(item as User).id"
                    @click="openEditModel(item)"
                  >
                    <PencilIcon class="size-4" />
                    <span class="sr-only">Edit</span>
                  </Button>
                  <Button variant="outline" size="sm" :id="(item as User).id">
                    <TrashIcon class="size-4" />
                    <span class="sr-only">Delete</span>
                  </Button>
                </template>
              </TBody>
            </template>
          </DataTable>
          <DynamicEdit
            :data="modelData"
            :columns="columns"
            :show="showEditModel"
            @close="closeModal"
            title="Edit User"
            @update="updateData"
          >
          </DynamicEdit>
        </div>
      </div>
    </div>
  </AppLayout>
</template>
