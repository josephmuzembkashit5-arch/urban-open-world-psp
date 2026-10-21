/*
 * Urban Open World PSP - prototype 0.1
 * Original homebrew starter for PSP / PPSSPP.
 * Uses PSP SDK (pspsdk) and does NOT contain GTA V assets.
 */
#include <pspkernel.h>
#include <pspdisplay.h>
#include <pspctrl.h>
#include <pspgu.h>
#include <pspgum.h>
#include <stdio.h>
#include <string.h>
#include <math.h>

PSP_MODULE_INFO("UrbanOpenWorld", 0, 0, 1);
PSP_MAIN_THREAD_ATTR(THREAD_ATTR_USER | THREAD_ATTR_VFPU);

#define BUF_WIDTH 512
#define SCR_WIDTH 480
#define SCR_HEIGHT 272
#define CENTER_X 240
#define CENTER_Y 136

static unsigned int __attribute__((aligned(16))) list[262144];
static unsigned int __attribute__((aligned(16))) framebuffer[BUF_WIDTH * SCR_HEIGHT];

typedef struct {
    float x, y, z;
    unsigned int color;
} Vertex;

static float player_x = 0.0f;
static float player_z = 0.0f;
static int camera_mode = 0;
static int mission_step = 0;
static int wanted_level = 0;

static void draw_rect(float x, float y, float w, float h, unsigned int color) {
    Vertex v[2] = {
        {x,     y,     0.0f, color},
        {x + w, y + h, 0.0f, color}
    };
    sceGuDisable(GU_TEXTURE_2D);
    sceGuColor(color);
    sceGuDrawArray(GU_SPRITES, GU_VERTEX_32BITF | GU_COLOR_8888 | GU_TRANSFORM_2D,
                   2, 0, v);
}

static void draw_quad(float x1, float y1, float x2, float y2,
                      float x3, float y3, float x4, float y4,
                      unsigned int color) {
    Vertex v[4] = {
        {x1,y1,0,color}, {x2,y2,0,color},
        {x3,y3,0,color}, {x4,y4,0,color}
    };
    sceGuColor(color);
    sceGuDrawArray(GU_TRIANGLE_STRIP,
        GU_VERTEX_32BITF | GU_COLOR_8888 | GU_TRANSFORM_2D, 4, 0, v);
}

static void draw_scene(void) {
    sceGuStart(GU_DIRECT, list);
    sceGuClearColor(0xff8797a4);
    sceGuClearDepth(0);
    sceGuClear(GU_COLOR_BUFFER_BIT | GU_DEPTH_BUFFER_BIT);

    sceGuDisable(GU_DEPTH_TEST);
    sceGuDisable(GU_CULL_FACE);
    sceGuDisable(GU_TEXTURE_2D);
    sceGuShadeModel(GU_SMOOTH);
    sceGuColor(0xff8797a4);

    /* Sky and distant skyline */
    draw_rect(0, 0, 480, 115, 0xff8797a4);
    draw_rect(0, 92, 480, 35, 0xff6c777d);

    /* Buildings on both sides of the road */
    draw_quad(0, 45, 116, 72, 145, 164, 0, 180, 0xff42484c);
    draw_quad(12, 65, 101, 80, 126, 154, 12, 166, 0xff62696d);
    draw_quad(480, 52, 365, 72, 340, 164, 480, 180, 0xff353b40);
    draw_quad(466, 72, 382, 84, 355, 154, 466, 166, 0xff555d62);

    /* Simple window rows */
    for (int row = 0; row < 5; row++) {
        for (int col = 0; col < 5; col++) {
            draw_rect(22 + col * 16, 83 + row * 14, 7, 6, 0xffc6b99a);
            draw_rect(392 + col * 13, 87 + row * 13, 6, 6, 0xffb7c3c8);
        }
    }

    /* Road, pavement and lane markings */
    draw_quad(0, 176, 480, 176, 480, 272, 0, 272, 0xff34383b);
    draw_quad(0, 169, 145, 164, 130, 190, 0, 202, 0xffa6a6a0);
    draw_quad(480, 169, 340, 164, 355, 190, 480, 202, 0xffa6a6a0);
    draw_quad(236, 183, 244, 183, 248, 207, 232, 207, 0xffe8dfb9);
    draw_quad(232, 220, 248, 220, 253, 249, 227, 249, 0xffe8dfb9);
    draw_quad(227, 259, 253, 259, 256, 272, 224, 272, 0xffe8dfb9);

    /* Player avatar: changes apparent framing with camera mode */
    if (camera_mode == 0) {
        draw_rect(224, 163, 32, 54, 0xff202a32);
        draw_rect(229, 145, 22, 22, 0xffb77f5b);
        draw_rect(221, 169, 9, 35, 0xff293b4c);
        draw_rect(250, 169, 9, 35, 0xff293b4c);
    } else if (camera_mode == 1) {
        draw_rect(231, 177, 18, 34, 0xff202a32);
        draw_rect(234, 165, 12, 14, 0xffb77f5b);
    } else {
        draw_rect(230, 181, 20, 30, 0xff202a32);
        draw_rect(234, 169, 12, 13, 0xffb77f5b);
        draw_rect(209, 211, 62, 14, 0xff171b1e); /* vehicle placeholder */
        draw_rect(214, 222, 13, 7, 0xff111111);
        draw_rect(254, 222, 13, 7, 0xff111111);
    }

    /* Minimal minimap panel and player marker */
    draw_rect(12, 190, 108, 70, 0xdd101619);
    draw_rect(18, 197, 96, 56, 0xff4e5a53);
    draw_rect(62, 197, 9, 56, 0xffb7b4a7);
    draw_rect(18, 221, 96, 6, 0xffb7b4a7);
    draw_rect(53, 219, 8, 8, 0xff42e7dc);

    /* HUD bars */
    draw_rect(132, 15, 82, 7, 0xff171717);
    draw_rect(134, 17, 68, 3, 0xff39c96a);
    draw_rect(132, 25, 82, 5, 0xff171717);
    draw_rect(134, 26, 57, 3, 0xffe4c64d);

    sceGuFinish();
    sceGuSync(0, 0);
    sceDisplayWaitVblankStart();
    sceGuSwapBuffers();
}

int main(void) {
    SceCtrlData pad;
    sceCtrlSetSamplingCycle(0);
    sceCtrlSetSamplingMode(PSP_CTRL_MODE_ANALOG);

    sceGuInit();
    sceGuStart(GU_DIRECT, list);
    sceGuDrawBuffer(GU_PSM_8888, (void*)0, BUF_WIDTH);
    sceGuDispBuffer(SCR_WIDTH, SCR_HEIGHT, (void*)0x88000, BUF_WIDTH);
    sceGuDepthBuffer((void*)0x110000, BUF_WIDTH);
    sceGuOffset(2048 - (SCR_WIDTH / 2), 2048 - (SCR_HEIGHT / 2));
    sceGuViewport(2048, 2048, SCR_WIDTH, SCR_HEIGHT);
    sceGuDepthRange(65535, 0);
    sceGuScissor(0, 0, SCR_WIDTH, SCR_HEIGHT);
    sceGuEnable(GU_SCISSOR_TEST);
    sceGuFinish();
    sceGuSync(0, 0);
    sceDisplayWaitVblankStart();
    sceGuDisplay(GU_TRUE);

    while (1) {
        sceCtrlReadBufferPositive(&pad, 1);

        if (pad.Buttons & PSP_CTRL_LEFT)  player_x -= 0.1f;
        if (pad.Buttons & PSP_CTRL_RIGHT) player_x += 0.1f;
        if (pad.Buttons & PSP_CTRL_UP)    player_z += 0.1f;
        if (pad.Buttons & PSP_CTRL_DOWN)  player_z -= 0.1f;

        /* Triangle cycles camera modes; Circle advances demo mission. */
        if (pad.Buttons & PSP_CTRL_TRIANGLE) {
            camera_mode = (camera_mode + 1) % 3;
            sceKernelDelayThread(180000);
        }
        if (pad.Buttons & PSP_CTRL_CIRCLE) {
            mission_step = (mission_step + 1) % 4;
            sceKernelDelayThread(180000);
        }
        if (pad.Buttons & PSP_CTRL_CROSS) {
            wanted_level = (wanted_level + 1) % 6;
            sceKernelDelayThread(180000);
        }

        draw_scene();
    }
    sceGuTerm();
    sceKernelExitGame();
    return 0;
}
