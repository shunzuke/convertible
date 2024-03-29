<template>
  <Stack :spaceing="16" direction="column">
    <Stack :spaceing="8" direction="column">
      <Stack :spaceing="8">
        <MenuTitle>フォーマット</MenuTitle>
        <MenuTitle>
          ( {{ getFormatName(editorTargetFile().originalFormat) }} →
          {{ getFormatName(data.format) }} )</MenuTitle
        >
      </Stack>
      <Stack :spaceing="8">
        <ChipButton :selected="getSelectedFormat('jpeg')" @click="() => clickFormat('jpeg')"
          >JPEG</ChipButton
        >
        <ChipButton :selected="getSelectedFormat('png')" @click="() => clickFormat('png')"
          >PNG</ChipButton
        >
        <ChipButton :selected="getSelectedFormat('gif')" @click="() => clickFormat('gif')"
          >GIF</ChipButton
        >
        <ChipButton :selected="getSelectedFormat('webp')" @click="() => clickFormat('webp')"
          >WebP</ChipButton
        >
        <ChipButton :selected="getSelectedFormat('avif')" @click="() => clickFormat('avif')"
          >AVIF</ChipButton
        >
      </Stack>
    </Stack>
    <Stack :spaceing="8" direction="column">
      <MenuTitle>レベル</MenuTitle>
      <Stack :spaceing="8">
        <ChipButton
          :selected="getSelectedFormatLevel('lossless')"
          :disabled="!getLosslessMode || isFormatUnchanged"
          @click="() => clickLevel('lossless')"
          >ロスレス</ChipButton
        >
        <ChipButton
          :selected="getSelectedFormatLevel('high')"
          :disabled="!getSelectedMode || isFormatUnchanged"
          @click="() => clickLevel('high')"
          >高品質</ChipButton
        >
        <ChipButton
          :selected="getSelectedFormatLevel('middle')"
          :disabled="isFormatUnchanged"
          @click="() => clickLevel('middle')"
          >標準品質</ChipButton
        >
        <ChipButton
          :selected="getSelectedFormatLevel('low')"
          :disabled="!getSelectedMode || isFormatUnchanged"
          @click="() => clickLevel('low')"
          >低品質</ChipButton
        >
      </Stack>
    </Stack>

    <Stack :spaceing="8" direction="column">
      <Switch
        :on="data.optimization"
        text="JPEG最適化"
        text-position="left"
        :disabled="!getOptimizationMode"
        aria-label="jpeg-optimization-switch"
        @click="clickOptimization"
        ><MenuTitle>JPEG最適化</MenuTitle></Switch
      >
      <Stack :spaceing="4" direction="column">
        <MenuSubText
          >JPEG最適化は、変換前・変換後ともにJPEGの場合に利用できる機能です。</MenuSubText
        >
        <MenuSubText>画質を維持したままファイルサイズの削減が期待できます。</MenuSubText>
      </Stack>
    </Stack>
  </Stack>
</template>

<script setup lang="ts">
  import {
    Format,
    FormatLevel,
    EditorSetttingValue,
    UpdataEditorSetttingValue,
  } from '~/types/index';

  interface Props {
    data: EditorSetttingValue;
  }

  const props = defineProps<Props>();

  const emit = defineEmits<{ (e: 'change', value: UpdataEditorSetttingValue): void }>();

  const { LOSSLESS } = useConstant();
  const { editorTargetFile } = useEditorTargetFile();
  const { getFormatName } = useGetFormatName();

  const isFormatUnchanged = computed(() => {
    return editorTargetFile().originalFormat === props.data.format;
  });

  const getSelectedFormat = computed(() => {
    return function (key: Format): boolean {
      return selectedFormat() === key;
    };
  });

  const getSelectedFormatLevel = computed(() => {
    return function (key: FormatLevel): boolean {
      return selectedFormatLevel() === key;
    };
  });

  const getOptimizationMode = computed(() => {
    return editorTargetFile().originalFormat === 'jpeg' && selectedFormat() === 'jpeg';
  });

  const getLosslessMode = computed(() => {
    return LOSSLESS.value.includes(selectedFormat());
  });

  const getSelectedMode = computed(() => {
    return !(selectedFormat() === 'png' || selectedFormat() === 'gif');
  });

  const selectedFormat = () => {
    return props.data.format;
  };

  const selectedFormatLevel = () => {
    return props.data.formatLevel;
  };

  const clickFormat = (key: Format) => {
    // 選択中のボタンを押したらスルー
    if (selectedFormat() === key) return;

    checkOptimiztion(key);

    emit('change', { format: key, formatLevel: 'middle' });
  };

  const checkOptimiztion = (key: Format) => {
    if (
      editorTargetFile().originalFormat === 'jpeg' &&
      selectedFormat() === 'jpeg' &&
      key !== 'jpeg'
    ) {
      emit('change', { optimization: false });
    }
  };

  const clickLevel = (key: FormatLevel) => {
    // 選択中のボタンを押したらスルー
    if (selectedFormatLevel() === key) return;

    emit('change', { formatLevel: key });
  };

  const clickOptimization = (value: boolean) => {
    emit('change', { optimization: !value });
  };
</script>

<style lang="scss" scoped></style>
