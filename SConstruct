import sys
import os
import excons
from excons.tools import gl

version = "2.1.0"

env = excons.MakeBaseEnv()
out_basedir = excons.OutputBaseDirectory()
out_incdir = os.path.join(out_basedir, "include")
out_libdir = os.path.join(out_basedir, "lib")

glew_name = "glew"
glew_static = excons.GetArgument("glew-static", 1, int) != 0

defs = ["GLEW_NO_GLU"]
if glew_static:
    defs.append("GLEW_STATIC")
else:
    defs.append("GLEW_BUILD")
    defs.append("glew_EXPORTS")

flags = ""
linkflags = ""
customs = [gl.Require]
libs = []

srcs = ["src/glew.c"]

prjs = [{"name": glew_name,
         "type": "staticlib" if glew_static else "sharedlib",
         "alias": "glew",
         "defs": defs,
         "cppflags": flags,
         "linkflags": linkflags,
         "incdirs": out_incdir,
         "libdirs": [],
         "libs": libs,
         "srcs": srcs,
         "version": version,
         "rpath": out_libdir,
         "custom": customs,
         "install": {out_incdir: excons.Glob("include/GL")}}]

def GlewName():
    libname = "glew"
    if sys.platform == "win32" and glew_static:
        libname = "lib" + libname
    return libname

def GlewPath():
    name = GlewName()
    if sys.platform == "win32":
        libname = name + ".lib"
    else:
        libname = "lib" + name + (".a" if glew_static else excons.SharedLibraryLinkExt())

    return out_libdir + "/" + libname

def RequireGlew(env):
    gl.Require(env)
    env.Append(CPPPATH=[out_incdir])
    env.Append(LIBPATH=[out_libdir])
    env.Append(CPPDEFINES=["GLEW_NO_GLU"])

    if glew_static:
        gl.Require(env)
        env.Append(LIBS=libs)
        env.Append(CPPDEFINES=["GLEW_STATIC"])

    excons.Link(env, GlewPath(), static=glew_static, force=True, silent=True)

excons.AddHelpOptions(USD="""GLEW OPTIONS
  glew-static=0|1          : Toggle between static and shared library build [1]""")

targets = excons.DeclareTargets(env, prjs)

Export("GlewName GlewPath RequireGlew")
