<template>
    <div class="ww-webapp-sidebar" :class="{ editing: isEditing }" :style="cssVariables">
        <div v-if="!isTopOrBottom" class="content">
            <div class="header">
                <wwElement
                    class="header__content"
                    :class="{ hidden: !isOpen }"
                    v-bind="content.headerContent"
                ></wwElement>
                <div @click="toggleSidebar">
                    <wwElement class="header__logo" v-bind="content.headerLogo"></wwElement>
                </div>
            </div>
            <wwElement class="content__layout" v-bind="content.contentLayout"></wwElement>
            <wwElement
                v-if="content.fixedBottomLayout"
                class="content__layout-bottom"
                v-bind="content.contentLayoutBottom"
            ></wwElement>
        </div>
        <div v-else class="content-mobile">
            <wwElement class="content-mobile__layout" v-bind="content.contentLayout"></wwElement>
        </div>

        <!-- wwEditor:start -->
        <div class="ww-webapp-sidebar__menu">
            <wwEditorIcon small name="menu" />
        </div>
        <!-- wwEditor:end -->
    </div>
</template>

<script>
export default {
    props: {
        content: { type: Object, required: true },
        /* wwEditor:start */
        wwEditorState: { type: Object, required: true },
        /* wwEditor:end */
    },
    emits: ['update:content'],
    data() {
        return {
            isOpen: false,
        };
    },
    computed: {
        isEditing() {
            /* wwEditor:start */
            return this.wwEditorState.editMode === wwLib.wwEditorHelper.EDIT_MODES.EDITION;
            /* wwEditor:end */
            // eslint-disable-next-line no-unreachable
            return false;
        },
        isTopOrBottom() {
            return this.content.positioning === 'top' || this.content.positioning === 'bottom';
        },
        sidebarWidth() {
            return this.isOpen === true ? this.content.widthOpen : this.content.widthClosed;
        },
        positioningValues() {
            switch (this.content.positioning) {
                case 'left':
                    return {
                        top: this.content.spacings,
                        left: this.content.spacings,
                        bottom: this.content.spacings,
                        width: this.sidebarWidth,
                    };
                case 'right':
                    return {
                        top: this.content.spacings,
                        right: this.content.spacings,
                        bottom: this.content.spacings,
                        width: this.sidebarWidth,
                    };
                case 'bottom':
                    return {
                        right: this.content.spacings,
                        left: this.content.spacings,
                        bottom: this.content.spacings,
                        height: this.content.heightBottomTop,
                        width: 'auto',
                    };
                case 'top':
                    return {
                        right: this.content.spacings,
                        left: this.content.spacings,
                        top: this.content.spacings,
                        height: this.content.heightBottomTop,
                        width: 'auto',
                    };
                default:
                    return {
                        top: this.content.spacings,
                        left: this.content.spacings,
                        bottom: this.content.spacings,
                        width: this.sidebarWidth,
                    };
            }
        },
        cssVariables() {
            const variables = {
                ...this.positioningValues,
                '--widthOpen': this.content.widthOpen,
                '--headerJustify': this.isOpen === true ? 'space-between' : 'center',
                '--transitionDuration': this.isEditing ? '0ms' : this.content.transitionDuration,
                '--transitionTimingFunction': this.content.transitionTimingFunction,
                '--shadows': this.content.shadows,
                '--flexDirection': this.isTopOrBottom ? 'row' : 'column',
            };

            return variables;
        },
    },
    methods: {
        toggleSidebar() {
            this.isOpen = !this.isOpen;
        },
    },
};
</script>

<style lang="scss">
.ww-webapp-sidebar {
    position: fixed;
    background-color: inherit;
    border-radius: inherit;
    box-shadow: inherit;
    transition: all var(--transitionDuration) var(--transitionTimingFunction);

    .content {
        overflow: hidden;
        display: flex;
        flex-direction: column;
        width: 100%;
        height: 100%;

        &__layout {
            overflow-y: auto;
            height: 100%;
            width: var(--widthOpen) !important;
        }

        &__layout-bottom {
            overflow: hidden;
            width: var(--widthOpen) !important;
        }

        .header {
            display: flex;
            flex-direction: row;
            justify-content: var(--headerJustify);
            align-items: center;

            &__logo {
                min-width: 30px;
                min-height: 30px;
                transition: all var(--transitionDuration) var(--transitionTimingFunction);

                position: absolute;
                top: 0;
                right: 0;
            }

            &__content {
                width: 100%;
                transition: all var(--transitionDuration) var(--transitionTimingFunction);
                &.hidden {
                    opacity: 0;
                    transform: translateX(-150%);
                    transition: all calc(var(--transitionDuration) / 1.3) var(--transitionTimingFunction);
                }
            }
        }
    }

    /* wwEditor:start */
    &.editing:hover {
        & > .border {
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            border: 1px solid var(--ww-editor-color);
            pointer-events: none;
            z-index: 10;
        }
        > .ww-webapp-sidebar__menu {
            opacity: 1;
            border: 1px solid var(--ww-editor-color);
            pointer-events: all;
        }
    }
    &__menu {
        display: flex;
        position: absolute;
        top: 0px;
        left: 5px;
        transform: translate(-50%, -50%);
        border-radius: 100%;
        padding: var(--ww-spacing-01);
        transition: opacity 0.2s ease;
        z-index: 101;
        cursor: pointer;
        background-color: var(--ww-color-blue-500);
        color: var(--ww-color-white);
        justify-content: center;
        align-items: center;
        opacity: 0;
        pointer-events: none;
        &:after {
            content: '';
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%) rotate(45deg);
        }
    }
    /* wwEditor:end */
}
</style>
