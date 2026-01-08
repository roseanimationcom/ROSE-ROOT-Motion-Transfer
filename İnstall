import maya.cmds as cmds

WINDOW_NAME = "ROSERootMotionUI"

TRANSFER_ATTRS = [
    ("translateX", "translateX"),
    ("translateZ", "translateZ"),
    ("rotateX", "rotateX"),
    ("rotateY", "rotateY"),
    ("rotateZ", "rotateZ"),
]

def create_ui():
    if cmds.window(WINDOW_NAME, exists=True):
        cmds.deleteUI(WINDOW_NAME)

    cmds.window(WINDOW_NAME, title="ROSE Root Motion", widthHeight=(330, 200))
    cmds.columnLayout(adjustableColumn=True, rowSpacing=8)

    cmds.text(label="Pelvis / Control Seç")
    cmds.rowLayout(nc=2, cw2=(220, 80))
    cmds.textField("controlField")
    cmds.button(label="Seç", command=select_control)
    cmds.setParent("..")

    cmds.text(label="Root Kemik Seç")
    cmds.rowLayout(nc=2, cw2=(220, 80))
    cmds.textField("jointField")
    cmds.button(label="Seç", command=select_joint)
    cmds.setParent("..")

    cmds.separator(height=10)
    cmds.button(
        label="START - Root Motion Transfer",
        height=40,
        bgc=(0.2, 0.6, 0.2),
        command=transfer_animation
    )

    cmds.showWindow(WINDOW_NAME)


def select_control(*args):
    sel = cmds.ls(sl=True)
    if sel:
        cmds.textField("controlField", e=True, text=sel[0])


def select_joint(*args):
    sel = cmds.ls(sl=True)
    if sel:
        cmds.textField("jointField", e=True, text=sel[0])


def transfer_animation(*args):
    control = cmds.textField("controlField", q=True, text=True)
    root = cmds.textField("jointField", q=True, text=True)

    if not cmds.objExists(control) or not cmds.objExists(root):
        cmds.warning("Control ve Root kemiği geçerli değil!")
        return

    start = int(cmds.playbackOptions(q=True, min=True))
    end = int(cmds.playbackOptions(q=True, max=True))

    for src_attr, dst_attr in TRANSFER_ATTRS:
        src = f"{control}.{src_attr}"
        dst = f"{root}.{dst_attr}"

        if not cmds.objExists(src) or not cmds.objExists(dst):
            continue

        # Var olan keyleri sil
        cmds.cutKey(dst, t=(start, end), clear=True)

        # Anim curve var mı?
        curves = cmds.listConnections(src, type="animCurve")
        if not curves:
            continue

        # Keyleri kopyala
        cmds.copyKey(src, t=(start, end))

        # Root’a yapıştır
        cmds.pasteKey(
            dst,
            option="replaceCompletely"
        )

    # Translate Y'yi sıfırla (güvenlik için)
    if cmds.objExists(f"{root}.translateY"):
        cmds.cutKey(f"{root}.translateY", t=(start, end), clear=True)
        cmds.setAttr(f"{root}.translateY", 0)

    cmds.confirmDialog(
        title="Tamamlandı",
        message="Root Motion başarıyla aktarıldı!\n(Y ekseni alınmadı)",
        button=["OK"]
    )


create_ui()
